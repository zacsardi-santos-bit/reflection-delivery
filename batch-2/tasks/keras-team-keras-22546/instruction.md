Implement a function and a class to find unique elements of an array within the Keras numpy-compatible operations module. Ensure the function and class handle both concrete arrays and symbolic tensors, support various options for sorting, axis specification, and output size, and correctly manage NaN values and empty arrays.

*   Implement the `unique` function in `keras/src/ops/numpy.py` with the following signature:
    *   `unique(x, return_inverse=False, return_counts=False, axis=None, sorted=True, size=None, fill_value=None) -> tensor or tuple`
    *   Return sorted unique elements by default.
    *   Flatten the input and return a 1D tensor when `axis=None`.
    *   Return inverse indices when `return_inverse=True`.
    *   Return counts of each unique element when `return_counts=True`.
    *   Return a tuple of (values, inverse, counts) when both `return_inverse` and `return_counts` are True.
    *   Support axis-specific unique finding for axis=0, axis=1, or their negative equivalents.
    *   Handle NaN values as distinct unique elements.
    *   Manage empty arrays without errors, returning empty results.
    *   Support multi-dimensional arrays when used with an axis parameter.
    *   Use the `size` parameter to fix output length, padding with `fill_value` or truncating as needed.
    *   Allow unsorted results when `sorted=False`.

*   Implement the `Unique` class in `keras/src/ops/numpy.py` with the following characteristics:
    *   Constructor parameters: `return_inverse=False, return_counts=False, axis=None, sorted=True, size=None, fill_value=None`.
    *   Provide a `.call(x)` method that mirrors the `unique` function's behavior and return semantics.
    *   Support symbolic KerasTensor inputs for shape inference.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.