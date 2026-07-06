Implement two new configuration controls for the ROCm all-reduce optimization in vLLM. These controls will allow users to override the minimum tensor size for optimized communication and define a threshold for when quantization should be applied. Use environment variables for configuration and ensure proper validation.

*   Update the `vllm/distributed/device_communicators/quick_all_reduce.py` module:
    *   Export byte-size constants: `KB` (equal to 1024) and `MB` (equal to 1024 * KB).
    *   Export the `QuickReduceRegime` enum with at least `INT4` and `FP` members, each having a `.value` attribute.
    *   Modify the `QuickAllReduce` class to include:
        *   A `should_quick_allreduce(inp: torch.Tensor) -> bool` method:
            *   Use `qr_min_size` as the lower bound if set; otherwise, use the built-in lookup table.
            *   Ensure `qr_quantization_min_size` does not affect eligibility.
        *   A static method `_get_qr_min_size(qr_max_size: int) -> int | None`:
            *   Read `VLLM_ROCM_QUICK_REDUCE_MIN_SIZE_BYTES_MB` from the environment.
            *   Return `None` if unset; otherwise, return `N * MB`.
            *   Raise `ValueError` for negative values or if the value exceeds `qr_max_size`.
        *   A static method `_get_qr_quantization_min_size() -> int | None`:
            *   Read `VLLM_ROCM_QUICK_REDUCE_QUANTIZATION_MIN_SIZE_KB` from the environment.
            *   Return `None` if unset; otherwise, return `N * KB`.
            *   Raise `ValueError` for negative values.
        *   An instance method `_get_qr_quant_level(inp: torch.Tensor) -> int`:
            *   Return `qr_quant_level.value` if `qr_quantization_min_size` is `None`.
            *   Return `QuickReduceRegime.FP.value` if the tensor's byte size is less than `qr_quantization_min_size`.
            *   Otherwise, return `qr_quant_level.value`.
        *   Ensure `quick_all_reduce(inp: torch.Tensor, *, out: torch.Tensor = None) -> torch.Tensor`:
            *   Pass the result of `_get_qr_quant_level(inp)` as the `quant_level` argument to `ops.qr_all_reduce`.

*   Update `vllm/envs.py`:
    *   Define and export two environment variables:
        *   `VLLM_ROCM_QUICK_REDUCE_MIN_SIZE_BYTES_MB`: Defaults to `None` when unset.
        *   `VLLM_ROCM_QUICK_REDUCE_QUANTIZATION_MIN_SIZE_KB`: Defaults to `None` when unset.
    *   Implement and export `disable_envs_cache()` to clear cached environment-variable values.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.