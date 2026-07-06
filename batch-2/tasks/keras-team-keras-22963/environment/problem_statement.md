## Description

Several tensor operations that accept an axis parameter do not validate whether the axis value is within bounds for the given tensor's dimensions during symbolic graph construction. When a developer passes an out-of-range axis value while building a model or computation graph with symbolic tensors, no error is raised at that point — the invalid axis silently passes through and only causes problems later during actual execution.

## Expected Behavior

- When an axis value exceeds the number of dimensions of the tensor, an error should be raised immediately during graph construction, clearly identifying the offending axis value and stating that it is out of bounds.
- This validation should apply to certain activation, normalization, and axis manipulation operations.
- The error message should include the specific axis value that was out of range and indicate it is out of bounds.

## Current Behavior

When constructing computation graphs with symbolic tensors and providing an axis value that is larger than the tensor's number of dimensions, no error is raised. This makes it harder to detect incorrect axis specifications early, forcing developers to wait until runtime to discover the issue.

## Why This Matters

Early validation of axis parameters makes debugging much easier. Developers should be able to catch invalid axis values at graph-construction time rather than during potentially expensive training or inference runs. A clear, descriptive error message pointing to the specific axis value helps quickly identify and fix the problem.
