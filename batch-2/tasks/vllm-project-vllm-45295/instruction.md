Implement a utility to handle misaligned weight shapes for quantized models using tensor parallelism. Ensure that weight dimensions are padded to meet Marlin kernel requirements, and provide functions to manage these padded dimensions and verify layer compatibility.

*   Implement `marlin_padded_nk(size_n: int, size_k: int, group_size: int) -> tuple[int, int]` in `vllm/model_executor/layers/quantization/utils/marlin_utils.py`.
    *   Return a tuple `(padded_n, padded_k)` where both dimensions are at least as large as the input and satisfy Marlin tile alignment.
    *   Ensure `(padded_n % 64 == 0 and padded_k % 128 == 0)` or `(padded_n % 128 == 0 and padded_k % 64 == 0)`.
    *   If `group_size > 0`, ensure `padded_k` is divisible by `group_size`.
    *   Return `(size_n, size_k)` unchanged if already aligned.

*   Implement `marlin_repacked_nk(repacked: torch.Tensor, num_bits: int) -> tuple[int, int]` in the same file.
    *   Derive `(padded_n, padded_k)` from a repacked weight tensor's shape.
    *   Ensure compatibility with `num_bits` values of 4 and 8.

*   Implement `marlin_pad_qweight(qweight: torch.Tensor, size_n: int, size_k: int, padded_n: int, padded_k: int) -> torch.Tensor`.
    *   Pad the `qweight` tensor from shape `(size_k // 8, size_n)` to `(padded_k // 8, padded_n)`.
    *   Fill added rows and columns with zeros.

*   Implement `marlin_pad_scales(scales: torch.Tensor, size_n: int, size_k: int, padded_n: int, padded_k: int, group_size: int) -> torch.Tensor`.
    *   For `group_size > 0`, transform input shape `(size_k // group_size, size_n)` to `(padded_k // group_size, padded_n)`, zero-filling beyond `size_n`.
    *   For `group_size == -1`, transform input shape `(1, size_n)` to `(1, padded_n)`.

*   Update `check_marlin_supports_layer(layer, group_size: int, allow_tile_padding: bool = False) -> bool`.
    *   With `allow_tile_padding=False`, maintain current behavior: return `False` for tile-misaligned shapes.
    *   With `allow_tile_padding=True`, return `True` for tile-misaligned shapes if `input_size_per_partition` is divisible by `group_size` or if `group_size == -1`.
    *   Return `False` if `input_size_per_partition` is not divisible by `group_size`.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.