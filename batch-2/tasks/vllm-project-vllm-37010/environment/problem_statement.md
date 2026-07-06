## Description

When using certain distributed inference backends with Mixture-of-Experts (MoE) models, the hidden dimension of expert weight buffers must be padded to a larger size than the original model checkpoint contains. For example, a model with a hidden size of 2688 may need its weight buffers rounded up to 3072 to satisfy backend alignment requirements. Currently, the weight-loading logic has no way to handle this mismatch: the in-memory parameter tensors are larger than the checkpoint tensors, so loading fails or silently produces incorrect weights.

## Expected Behavior

- When loading checkpoint weights into padded parameter buffers, the real weights should be placed at the start of the buffer and the padding region should remain zeroed.
- A helper should be available to determine which dimension of a weight tensor is the "hidden" dimension, given the tensor's number of dimensions and its shard dimension. This is needed because weight tensors for different roles (e.g., w1, w2, w3, transposed variants) store the hidden dimension at different axes.
- The narrowing helper should be a no-op when no size mismatch exists, when the loaded tensor is a scalar, or when a negative sentinel value is passed for the hidden dimension.
- When using a quantization scheme that is fundamentally incompatible with hidden-dimension padding, weight loading should raise a clear error rather than silently producing corrupt weights.

## Why This Matters

Models like nemotron_h used with DeepEP or NIXL EP backends require this padding behavior to operate. Without this fix, loading such models either fails outright or loads incorrect weights, making these backend/model combinations unusable. With the fix, the padded hidden dimension is handled transparently during checkpoint loading, while incompatible quantization schemes surface a clear actionable error.
