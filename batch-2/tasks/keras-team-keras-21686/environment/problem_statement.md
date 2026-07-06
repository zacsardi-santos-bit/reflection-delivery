## Description

The 4-bit integer packing and unpacking utilities currently only support signed 8-bit integer tensors. This is overly restrictive: unsigned 8-bit integers are a natural and common storage format for packed 4-bit values in the range [0, 15], and several quantization workflows rely on unsigned storage. Right now, passing unsigned tensors to these utilities causes a type error, and there is no way to pack or unpack values while retaining an unsigned representation.

Additionally, when applying a post-training quantization algorithm with 4-bit weight precision to a fully-connected or einsum-based dense layer, the quantized weights are not being stored compactly. Two 4-bit values should be packed into a single byte (halving the kernel's parameter count), but this packing is currently not happening. As a result, the layer's kernel property also returns the wrong data — it currently returns the raw packed bytes instead of unpacking them back to the correct shape and value range.

## Expected Behavior

- The packing and unpacking utilities should accept an optional dtype parameter so callers can choose between signed and unsigned byte representation.
- When using signed mode, unpacked values should fall in the range [-8, 7]; when using unsigned mode, values should remain in [0, 15].
- A pack-then-unpack round trip must recover the original tensor exactly, for both signed and unsigned modes, across various tensor shapes and axes.
- After applying 4-bit post-training quantization to a fully-connected or einsum-based dense layer, the stored quantized kernel must be exactly half the size of the original (two 4-bit values packed per byte).
- After calibration, accessing the kernel property on a 4-bit quantized layer must return the unpacked form of the stored kernel, not the raw packed bytes.

## Why This Matters

These changes are necessary to support memory-efficient 4-bit weight quantization in practice. Without them, models that use this quantization path waste memory (no packing), and code inspecting or using the kernel property gets incorrect values.
