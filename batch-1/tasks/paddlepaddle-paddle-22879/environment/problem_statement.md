## Description

Dynamic graph tensors currently have very limited support for indexing and slicing. When working in eager execution mode, developers cannot use the full range of standard array-compatible indexing patterns that they would expect to work on tensors. This makes it difficult to write natural, Pythonic code in dynamic graph mode without constantly converting tensors to plain arrays first.

## Expected Behavior

- Indexing a tensor with a single integer should reduce the dimension and return a tensor with the correct shape.
- Full-range slices on one or more dimensions should preserve the tensor's shape.
- Negative indices should correctly select elements from the end of a dimension.
- Scalar indexing across all dimensions should return a 1-element 1D tensor.
- Slices with negative stops should correctly clip to the appropriate elements.
- Negative step values (i.e., reversing along a dimension) should work both on individual dimensions and when combined across multiple dimensions.
- Mixed combinations of integer and range indices should correctly reduce or preserve each dimension.
- Out-of-bounds stop values in a range should not raise errors — they should be clipped to the actual dimension size.
- All these slicing results must be numerically identical to what standard array indexing would produce for the equivalent operation on the same data.
- Slicing should continue to work correctly on tensors that have been reshaped.

## Why This Matters

Users who write code in dynamic graph (eager) mode need to be able to index and slice tensors using the same intuitive syntax they would use with standard numerical arrays. Without full slice support, developers are forced to add manual conversions that break the flow of their code and make it much harder to experiment and iterate in dynamic graph mode.
