## Description

The Keras numpy operations module is missing an operation to find the unique elements of an array or along a specified axis. This is a standard numerical computing operation present in numpy but currently unavailable in Keras's numpy-compatible ops layer.

## Expected Behavior

- Users should be able to find unique elements of an array (with optional flattening), or unique slices along a specific axis.
- The operation should support returning inverse indices — which map each position in the original array back to its corresponding unique element — as well as element occurrence counts.
- The operation should work with both eager execution (concrete arrays) and symbolic tensor computation (shape inference during graph construction).
- When given a fixed output size, the operation should pad results with a configurable fill value when fewer unique elements exist than requested, or truncate when more exist. For symbolic tensors, a provided size should result in a statically known output shape.
- The operation should support an option to return results in unsorted order.
- Each NaN value in the input should be treated as a distinct unique element.

## Why This Matters

Without this operation, users cannot perform unique-element finding within Keras's op graph in a way that integrates correctly with shape inference and backend dispatch. This is a common operation in data preprocessing and model construction, so its absence forces users to fall back to framework-specific or external code.
