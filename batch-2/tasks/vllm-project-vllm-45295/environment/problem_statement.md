## Description

When serving large quantized models (such as Nemotron-Super-120B) with tensor parallelism across multiple GPUs, the per-device weight matrix shapes often don't satisfy the alignment requirements of the Marlin quantized GEMM kernel. For example, a projection layer's weight that is valid at TP=1 can produce per-shard dimensions at TP=4 like (4640, 512) that are tile-misaligned, causing the Marlin layer check to reject the layer entirely even though only a small amount of zero-padding is needed to make the shape compatible.

## Expected Behavior

- A new utility function should compute the minimal tile-aligned and group-size-aligned (padded N, padded K) shape that is at least as large as the original unaligned shape. Shapes that are already aligned must pass through unchanged to avoid any overhead on the common path.
- A companion function should recover the original padded dimensions from an already-repacked quantized weight tensor, enabling consistent shape handling throughout the quantization pipeline.
- New helper functions should zero-pad a packed quantized weight tensor and a scales tensor to the padded dimensions, ensuring that the padded regions contribute nothing to the matrix multiply output.
- The layer compatibility check function should gain an opt-in mode that allows tile-misaligned shapes when padding can make them valid. Shapes where the group size does not evenly divide the rank-local K dimension (meaning quantization groups would straddle the tensor-parallel shard boundary) must still be rejected even in this mode.

## Why This Matters

Without this support, models that produce non-tile-aligned per-GPU weight shapes at common tensor-parallel configurations fail to use Marlin kernels for quantized inference, falling back to slower paths or erroring out entirely. This fix unblocks these models while preserving full numerical accuracy by ensuring the padded columns carry zero weight in the output.
