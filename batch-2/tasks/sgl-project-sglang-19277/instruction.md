I'm building debugging utilities for distributed model inference and I need to add functionality to reassemble sharded tensors from multiple worker ranks back into a single full tensor.

*   The AxisInfo class must have integer fields axis_rank and axis_size, support equality comparison, and be immutable (frozen).

*   The UnshardPlan class must have fields axis (a ParallelAxis enum value), params (an object with an integer dim field), and world_ranks_by_axis_rank (a list of integers), and be immutable (frozen).

*   The normalize_parallel_info function must accept a dictionary (meta) and return a dict mapping ParallelAxis enum values to AxisInfo instances. It must recognize both 'sglang_parallel_info' and 'megatron_parallel_info' keys. It must extract 'tp_rank'/'tp_size' into ParallelAxis.TP and 'cp_rank'/'cp_size' into ParallelAxis.CP. Axes with axis_size equal to 1 must be excluded from the result.

*   The normalize_parallel_info function must return an empty dict when the meta contains neither 'sglang_parallel_info' nor 'megatron_parallel_info' keys, or when those keys are absent.

*   The normalize_parallel_info function must raise a ValueError whose message contains the substring 'multiple parallel_info' when both 'sglang_parallel_info' and 'megatron_parallel_info' keys are present in the meta dict.

*   The compute_unshard_plan function must raise a ValueError whose message contains the substring 'must not be empty' when the parallel_infos list is empty.

*   The compute_unshard_plan function must return None when no dimension in dim_specs is annotated with a parallel axis.

*   The compute_unshard_plan function must return an UnshardPlan with: axis equal to the sharded ParallelAxis, params.dim equal to the zero-based index of the sharded dimension in dim_specs, and world_ranks_by_axis_rank as a list mapping each axis rank (0..N-1) to the corresponding world rank.

*   The compute_unshard_plan function must correctly compute world_ranks_by_axis_rank even when the input parallel_infos list is provided in world-rank order that does not match axis-rank order (i.e., scrambled world ranks).

*   The compute_unshard_plan function must raise a ValueError whose message contains the substring 'Inconsistent axis_size' when different entries in parallel_infos report different axis_size values for the same sharded axis.

*   The compute_unshard_plan function must raise a ValueError whose message contains the substring 'No parallel_info found' when no entry in parallel_infos contains an entry for the sharded axis.

*   The compute_unshard_plan function must raise a NotImplementedError whose message contains the substring 'Multi-axis unshard' when dim_specs contains dimensions sharded across more than one distinct parallel axis.

*   The execute_unshard_plan function must accept an UnshardPlan and a dict mapping world ranks (int) to tensors, reorder the tensors according to world_ranks_by_axis_rank, and concatenate them along the dimension specified by plan.params.dim. The resulting tensor must be numerically identical to the original unsharded tensor.

*   The execute_unshard_plan function must correctly reconstruct the full tensor even when world ranks are scrambled relative to axis ranks.


*   Interface details: Type: Class
Name: AxisInfo
Location: python/sglang/srt/debug_utils/comparator/unshard/types.py
Description: Frozen immutable data class holding parallelism axis metadata for a single rank. Must support equality comparison.
Signature:
  axis_rank: int   # this rank's position within its parallelism axis
  axis_size: int   # total number of ranks along the axis

Type: Class
Name: UnshardPlan
Location: python/sglang/srt/debug_utils/comparator/unshard/types.py
Description: Frozen immutable data class representing a plan to reconstruct a full tensor from per-rank shards.
Signature:
  axis: ParallelAxis                  # the parallelism axis being unsharded
  params: UnshardParams               # operation parameters; must have an integer field `dim` indicating the tensor dimension to concatenate along
  world_ranks_by_axis_rank: list[int] # list of world ranks indexed by axis rank (index i gives world rank of axis rank i)

Type: Function
Name: normalize_parallel_info
Location: python/sglang/srt/debug_utils/comparator/unshard/parallel_info.py
Signature: normalize_parallel_info(meta: dict) -> dict[ParallelAxis, AxisInfo]
Description: Extracts unified parallelism axis information from a rank's metadata dictionary.
  - Reads from the key "sglang_parallel_info" (fields: tp_rank, tp_size, pp_rank, pp_size, etc.)
    or "megatron_parallel_info" (fields: tp_rank, tp_size, cp_rank, cp_size, dp_rank, dp_size, etc.)
  - Maps tp_rank/tp_size to ParallelAxis.TP and cp_rank/cp_size to ParallelAxis.CP.
  - Axes with axis_size == 1 are excluded from the result.
  - Returns {} when neither recognized key is present.
  - Raises ValueError with message containing "multiple parallel_info" if both recognized keys are present.

Type: Function
Name: compute_unshard_plan
Location: python/sglang/srt/debug_utils/comparator/unshard/planner.py
Signature: compute_unshard_plan(dim_specs: list[DimSpec], parallel_infos: list[dict[ParallelAxis, AxisInfo]]) -> Optional[UnshardPlan]
Description: Computes an UnshardPlan from tensor dimension specifications and per-rank parallel info.
  - Returns None when no dimension in dim_specs is annotated with a parallel axis.
  - Returns UnshardPlan with:
      axis = the sharded ParallelAxis enum value
      params.dim = zero-based index of the sharded dimension in dim_specs
      world_ranks_by_axis_rank = list mapping each axis rank (0..N-1) to its world rank
  - Correctly resolves world_ranks_by_axis_rank even when parallel_infos entries are not in axis-rank order.
  - Raises ValueError with message containing "must not be empty" when parallel_infos is [].
  - Raises ValueError with message containing "Inconsistent axis_size" when different entries report conflicting axis_size for the same axis.
  - Raises ValueError with message containing "No parallel_info found" when no entry contains the sharded axis.
  - Raises NotImplementedError with message containing "Multi-axis unshard" when dim_specs contains dimensions sharded across more than one distinct parallel axis.

Type: Function
Name: execute_unshard_plan
Location: python/sglang/srt/debug_utils/comparator/unshard/executor.py
Signature: execute_unshard_plan(plan: UnshardPlan, tensors_by_world_rank: dict[int, torch.Tensor]) -> torch.Tensor
Description: Executes an UnshardPlan to reconstruct a full tensor from per-rank shards.
  - Looks up each shard via tensors_by_world_rank using plan.world_ranks_by_axis_rank to establish axis-rank order.
  - Concatenates the ordered shards along plan.params.dim.
  - Returns a tensor numerically identical to the original unsharded tensor.
  - Handles scrambled world rank assignments (world rank order ≠ axis rank order).


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.