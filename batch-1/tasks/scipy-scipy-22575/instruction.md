Implement the `vectorized_filter` function in the `scipy.ndimage` module to apply user-defined functions over sliding windows of an array in a batched manner. Ensure it supports all existing boundary modes and introduces a new "valid" mode for unpadded outputs.

Requirements:

*   Implement `vectorized_filter` in `scipy/ndimage/_filters.py` and export it via `scipy/ndimage/__init__.py`.
    *   Function signature: `vectorized_filter(input, function, size=None, footprint=None, output=None, mode='reflect', cval=None, origin=None, axes=None, batch_memory=None)`.
*   Ensure compatibility with `generic_filter` for modes: 'reflect', 'nearest', 'mirror', 'wrap', 'constant'.
    *   Results must be numerically equivalent within a tolerance of 1e-15.
*   Implement support for a new mode 'valid', which returns only elements where the window fits entirely within the input.
    *   Output shape along filtered axes: `input.shape - window_size + 1`.
*   Implement a `batch_memory` parameter to control the memory usage for processing batches of windows.
    *   Raise `ValueError` with message "`batch_memory` is insufficient for minimum chunk size." if the memory is too low.
*   Ensure the input array remains unmodified.
*   Handle output array:
    *   Write results into the provided `output` array and cast to its dtype.
    *   Determine dtype by function's return type if no `output` is provided.
*   Handle zero-dimensional inputs and edge cases:
    *   Return an array with the same shape as input for 0-D inputs.
    *   Handle 1x1 window size by returning the input unchanged.
    *   Handle windows larger than the input appropriately.
*   Validate parameters and raise `ValueError` for invalid inputs:
    *   "`function` must be a callable." if `function` is not callable.
    *   "Either `size` or `footprint` must be provided." if neither is given.
    *   "Either `size` or `footprint` may be provided, not both." if both are given.
    *   "All elements of `size` must be positive integers." if any element is invalid.
    *   "The dimensionality of the window" if window dimensionality mismatches.
    *   "`axes` must be provided if the dimensionality..." if axes are not specified for 1-D size/footprint.
    *   "All elements of `origin` must be integers" if origin contains non-integers.
    *   "`origin` must be an integer or tuple of integers with length..." if origin length mismatches.
    *   "`mode` must be one of..." for unrecognized modes.
    *   "`mode='valid'` is incompatible with use of `origin`." if mode is 'valid' with non-zero origin.
    *   "Use of `cval` is compatible only with `mode='constant'`." if cval is used with non-constant mode.
    *   "`cval` must include only numbers." if cval is non-numeric.
    *   "`batch_memory` must be positive number." if batch_memory is invalid.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.