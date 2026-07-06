## Description

The tensor library is missing two common reduction operations: one that checks whether **any** element in a tensor is non-zero (or true for boolean tensors), and one that checks whether **all** elements are non-zero. These are fundamental operations found in most numerical computing libraries and are frequently needed when writing conditional logic, validation checks, or masking pipelines.

Currently, users who need to know "does this tensor contain at least one non-zero value?" or "are all of these values non-zero?" have no direct, idiomatic way to express this — they must work around the limitation with more complex expressions.

## Expected Behavior

- A global reduction operation that returns a single boolean value indicating whether any element satisfies the truthy condition (non-zero for floats and ints; true for booleans).
- A global reduction operation that returns a single boolean value indicating whether all elements satisfy the truthy condition.
- Dimension-wise variants of both operations, which reduce along a specified axis and return a boolean tensor with that axis collapsed to size 1.
- All four operations must work uniformly across floating-point, integer, and boolean tensor types.

## Why This Matters

These operations are essential building blocks for tensor-based conditional logic. Without them, users are forced to combine multiple operations to achieve what should be a single, readable call. Adding them makes the API more complete and consistent with the expectations of users coming from other numerical frameworks.
