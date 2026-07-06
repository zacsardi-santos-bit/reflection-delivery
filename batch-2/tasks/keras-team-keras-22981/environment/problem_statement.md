## Description

Several tensor operations do not validate whether the provided axis value is within the valid bounds for the tensor's number of dimensions. When an out-of-range axis is passed, the operations fail silently or produce confusing downstream errors instead of immediately raising a clear, descriptive error.

## Expected Behavior

- When an out-of-range axis value is passed to any of the following operations, an error should be raised immediately with a message that clearly identifies the invalid axis value and states it is out of bounds.
- This should apply consistently across: unstacking, concatenating, splitting, computing differences, indexing along an axis, and stacking tensors.
- The error message format should be consistent across all affected operations.

## Current Behavior

The operations either silently proceed with the invalid axis, or raise an unhelpful error that doesn't clearly indicate the axis value was out of bounds.

## Why This Matters

Consistent axis bounds checking makes it much easier to catch and debug mistakes early. Developers passing an invalid axis should immediately see which axis value was invalid and why, rather than having to trace confusing downstream failures.
