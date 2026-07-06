I'm working on the expert load balancing module in our inference framework and need help implementing two missing algorithms.

*   The `balanced_packing` function must accept a 2D weight tensor of shape [num_layers, n] and an integer `num_packs`, and return a tuple of two tensors `(pack_index, rank_in_pack)` both having the same shape as the input weight tensor.

*   All values in `pack_index` must be in [0, num_packs). Within each layer, every pack must appear exactly `n // num_packs` times (groups_per_pack = n // num_packs), ensuring each pack receives the same number of items.

*   All values in `rank_in_pack` must be in [0, groups_per_pack). Within each layer, every (pack_index, rank_in_pack) pair must be unique — each slot is occupied exactly once.

*   When num_packs equals n (groups_per_pack == 1), `pack_index[layer]` must be a permutation of [0, n) and all `rank_in_pack` values must be zero.

*   `balanced_packing` must implement a greedy load-balancing strategy: for input weight=[[9.0, 1.0, 1.0, 1.0]] with num_packs=2, the maximum total weight across all packs must be 10.0 (not 11.0), meaning the heaviest item must be paired with only one lighter item.

*   `balanced_packing` must be deterministic: calling it twice with the same input tensor must produce identical `pack_index` and `rank_in_pack` outputs.

*   The `compute_logical_to_rank_dispatch_physical_map` function must accept `server_args` (object with `ep_size` and `nnodes` integer attributes), `logical_to_all_physical_map` (int64 tensor of shape [num_layers, num_logical_experts, replicas_per_logical]), `ep_size` (int), `num_physical_experts` (int), `ep_rank` (int), and an optional `seed` parameter. It must return an int64 tensor of shape [num_layers, num_logical_experts].

*   All values in the output of `compute_logical_to_rank_dispatch_physical_map` must be valid physical expert IDs in [0, num_physical_experts). The output must contain no -1 sentinel values — every logical expert must be assigned a physical expert for every layer.

*   `compute_logical_to_rank_dispatch_physical_map` must implement locality-aware assignment: when ep_size=4 and nnodes=2 (2 GPUs per node), GPU 0 (node 0) querying for a logical expert whose replicas are both on GPU 0 must receive one of those local physical expert IDs; for a logical expert whose replicas are on GPU 1 (same node 0), GPU 0 must receive one of those same-node physical expert IDs.

*   Different `ep_rank` values must produce different physical expert assignments — not all ranks may return identical mappings.

*   `compute_logical_to_rank_dispatch_physical_map` must be deterministic with the same seed (calling twice with seed=7 yields identical results), and different seeds must produce potentially different assignments for remote experts (across at least 20 different seed values, at least two distinct result tensors must be produced for a GPU with remote experts).

*   `compute_logical_to_rank_dispatch_physical_map` must handle edge cases: single layer (num_layers=1), single node (nnodes=1, all GPUs on same node), and all physical experts being replicas of a single logical expert.


*   Interface details: Type: Function
Name: balanced_packing
Location: python/sglang/srt/eplb/eplb_algorithms/deepseek.py
Signature: balanced_packing(weight: torch.Tensor, num_packs: int) -> tuple[torch.Tensor, torch.Tensor]
Description: Assigns each expert in each layer to a pack and a rank within that pack, using a greedy weight-balancing strategy. The `weight` input is a 2D tensor of shape [num_layers, n]. Returns `(pack_index, rank_in_pack)`, both tensors of shape [num_layers, n]. `pack_index[i, j]` is the pack assigned to expert j in layer i (values in [0, num_packs)). `rank_in_pack[i, j]` is the rank of expert j within its assigned pack in layer i (values in [0, n // num_packs)). Each pack receives exactly `n // num_packs` experts per layer and every (pack, rank) slot is used exactly once per layer.

Type: Function
Name: compute_logical_to_rank_dispatch_physical_map
Location: python/sglang/srt/eplb/expert_location.py
Signature: compute_logical_to_rank_dispatch_physical_map(server_args, logical_to_all_physical_map: torch.Tensor, ep_size: int, num_physical_experts: int, ep_rank: int, seed: int = ...) -> torch.Tensor
Description: For a given GPU (`ep_rank`), computes which physical expert it should dispatch to for each (layer, logical_expert) pair. `server_args` must have `ep_size` (int) and `nnodes` (int) attributes. `logical_to_all_physical_map` is an int64 tensor of shape [num_layers, num_logical_experts, replicas_per_logical] listing all physical expert replicas for each logical expert. Returns an int64 tensor of shape [num_layers, num_logical_experts] with values in [0, num_physical_experts). The function assigns local replicas first (same GPU), then same-node replicas, then remote. The optional `seed` parameter controls randomness for tie-breaking among equally-close replicas.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.