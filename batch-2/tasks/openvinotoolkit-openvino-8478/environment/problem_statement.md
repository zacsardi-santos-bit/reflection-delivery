## Description

The model optimizer's shape inference for two tensor operations — element gathering and sparse tensor reshaping — does not correctly handle cases where some tensor dimensions are dynamic (unknown at compile time). When optimizing models that have variable-length or partially-unknown input shapes, the optimizer currently produces incorrect output shapes or fails entirely.

## Expected Behavior

- When performing shape inference on an element-gathering operation with dynamic dimensions in the indices tensor, the output shape should be computed correctly: for dimensions along the gather axis, use the indices dimension; for all other dimensions, use whichever of data or indices provides the concrete (non-dynamic) value.
- When performing shape inference on a sparse tensor reshape operation where some dimensions in the input shape or desired output shape are dynamic, the output shape should be inferred as precisely as possible — resolving any inferrable dimensions from the total element count when feasible, and leaving truly ambiguous dimensions as dynamic.
- When an element-gathering operation is given data and indices tensors with incompatible shapes at non-axis positions (both statically known but different), the optimizer should raise an error.
- When a sparse tensor reshape operation receives shapes with provably incompatible element counts, the optimizer should raise an error indicating that shape propagation was stopped.

## Why This Matters

Models with dynamic or partially-known shapes are common in production, particularly when input sequences have variable lengths. Without correct dynamic shape inference, the optimizer cannot process these models, preventing compilation. Proper validation errors also help developers identify misconfigured model graphs early.
