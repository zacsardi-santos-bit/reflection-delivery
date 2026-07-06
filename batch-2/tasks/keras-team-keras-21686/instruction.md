Implement the 4-bit integer packing and unpacking utilities to support both signed and unsigned byte representations. Ensure that the functions accept an optional parameter to choose the representation, defaulting to signed behavior, and that both the packed and unpacked tensors have the requested dtype. Additionally, ensure that quantized kernels for fully-connected and einsum-based dense layers are stored compactly and unpacked correctly when accessed.

Requirements:

*   Update `pack_int4` function in `keras/src/quantizers/quantizers.py`:
    *   Accept an optional `dtype` parameter with default value "int8".
    *   Allow only "int8" and "uint8" as valid `dtype` values; raise `ValueError` otherwise.
    *   Raise `TypeError` if the input tensor's standardized dtype does not match the `dtype` parameter.
    *   Ensure the packed output tensor has the same dtype as the `dtype` parameter.
    *   Return a tuple `(packed, packed_shape, orig_len)` where `packed` has dtype matching the `dtype` parameter.

*   Update `unpack_int4` function in `keras/src/quantizers/quantizers.py`:
    *   Accept an optional `dtype` parameter with default value "int8".
    *   Allow only "int8" and "uint8" as valid `dtype` values; raise `ValueError` otherwise.
    *   When `dtype="int8"`, convert unpacked nibbles [0, 15] to the signed range [-8, 7].
    *   When `dtype="uint8"`, keep unpacked nibbles as unsigned values [0, 15].
    *   Ensure the unpacked output tensor has the same dtype as the `dtype` parameter.

*   Ensure a round-trip pack-then-unpack operation recovers the original tensor exactly for both "int8" and "uint8" modes, across 2D and higher-rank tensors with positive and negative axis values.

*   For Dense layers quantized with 4-bit GPTQ (weight_bits=4):
    *   Store `quantized_kernel` in a packed format where two int4 values occupy a single byte.
    *   Ensure the total number of elements in `quantized_kernel` is exactly half the total number of elements in the original kernel.
    *   If `is_gptq_calibrated` is True, the `kernel` property must return the unpacked form of `quantized_kernel`.

*   For EinsumDense layers quantized with 4-bit GPTQ (weight_bits=4):
    *   Store `quantized_kernel` in a packed format with total elements equal to exactly half the total elements of the original kernel.
    *   If `is_gptq_calibrated` is True, the `kernel` property must return the unpacked form of `quantized_kernel`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.