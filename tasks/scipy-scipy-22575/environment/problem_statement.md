## Description

The image filtering module provides a way to apply user-defined functions to each sliding window of an array, but the current implementation passes one window at a time to the function. This is unnecessarily slow when the user's function naturally accepts batches of windows at once (like NumPy's built-in reductions), because all the overhead of iterating over windows happens in Python rather than in optimized native code.

A vectorized (batched) counterpart is needed that passes all sliding windows simultaneously to the callable, enabling much faster execution for functions that can operate over batched inputs.

## Expected Behavior

- The new filter function should accept the same window-specification options as the existing generic filter (size, footprint, axes, origin, and boundary modes).
- It should support all existing boundary/padding modes: reflect, nearest, mirror, wrap, and constant.
- It should additionally support a special "valid" boundary mode that returns only the output elements where the window falls entirely within the input, with no padding applied — the output is smaller than the input in that case.
- A memory-budget parameter should allow users to control how large each batch of windows can be, which is useful when working with large arrays and limited RAM. If the memory budget is too small to process even the minimum possible chunk, an error should be raised.
- When an output array is provided, the result should be written into it and cast to the output array's dtype.
- The input array must not be modified.

## Why This Matters

For filters that apply vectorizable functions like mean, sum, or other NumPy reductions, the existing approach is orders of magnitude slower than necessary. The vectorized version enables users to write fast custom filters without having to implement boundary handling or window extraction themselves.
