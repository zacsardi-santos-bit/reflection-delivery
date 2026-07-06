Implement support for loading checkpoints into Mixture-of-Experts models with padded expert weight buffers. Ensure that the real weights are correctly placed in the buffer, and handle any mismatches between in-memory and checkpoint tensor sizes. Provide helper methods to identify hidden dimensions and manage padding.

*   Implement a static method `_get_hidden_dim(shard_dim: int, ndim: int) -> int` in `vllm/model_executor/layers/fused_moe/layer.py`.
    *   Return the index of the hidden dimension based on `ndim` and `shard_dim`.
    *   For `ndim=1`, always return 0.
    *   For `ndim=2`, return 1 if `shard_dim=0`, else return 0.
    *   For `ndim=3`, return 2 if `shard_dim=1`, and 1 if `shard_dim=2`.
    *   Raise `ValueError` with the message 'not a valid data dimension' for `ndim=3` and `shard_dim=0`.

*   Implement a static method `_narrow_expert_data_for_padding(expert_data: torch.Tensor, loaded_weight: torch.Tensor, hidden_dim: int) -> torch.Tensor` in `vllm/model_executor/layers/fused_moe/layer.py`.
    *   Return a view of `expert_data` narrowed along `hidden_dim` to match `loaded_weight.shape[hidden_dim]` if `expert_data.shape[hidden_dim] > loaded_weight.shape[hidden_dim]`.
    *   Return `expert_data` unchanged if:
        *   `expert_data.shape[hidden_dim] <= loaded_weight.shape[hidden_dim]`.
        *   `loaded_weight.ndim == 0` (scalar tensor).
        *   `hidden_dim < 0`.
    *   Ensure only the `hidden_dim` axis is narrowed; other axes retain original sizes.
    *   Ensure the returned tensor shares storage with `expert_data` (is a view).

*   Update the `weight_loader` method in `vllm/model_executor/layers/fused_moe/layer.py`.
    *   Raise `ValueError` with a message matching 'BitsAndBytes' if `param` has `use_bitsandbytes_4bit=True` and there is a hidden-size mismatch between the in-memory padded parameter and the loaded checkpoint weight.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.