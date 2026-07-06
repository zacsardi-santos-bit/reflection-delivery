Implement the Adaptive Antoulas-Anderson (AAA) rational approximation algorithm in SciPy's interpolation module. Create a class that constructs a rational approximant from sample points and function values, and returns a callable object. Ensure the object can evaluate new points and expose analytic properties like poles, residues, and roots.

*   Implement the `AAA` class in `scipy/interpolate/__init__.py` and ensure it is importable from `scipy.interpolate`.
*   Define the constructor `AAA(z, f, rtol=<default>, max_terms=<default>)`:
    *   Accept two 1-D arrays `z` (sample points) and `f` (function values).
    *   Accept optional parameters `rtol` (relative tolerance) and `max_terms` (maximum terms).
    *   Raise `ValueError` with 'same size' if `z` and `f` have different lengths.
    *   Raise `ValueError` with '1-D' if `z` or `f` are not 1-D arrays.
    *   Raise `ValueError` with 'finite' if `z` contains non-finite values.
*   Ensure the AAA algorithm issues a `RuntimeWarning` with 'AAA failed' if it does not converge within `max_terms`.
*   Make the returned object callable:
    *   Evaluate it at new points `z2` to return an array of approximated values.
    *   Ensure evaluation returns `np.nan` for `np.nan` inputs and a finite value for `np.inf` inputs.
    *   Match the dtype of the output array to the input data dtype.
*   Provide attributes on the returned object:
    *   `support_points`: ndarray of selected support points, dtype matches input `z`.
    *   `support_values`: ndarray of function values at support points, dtype matches input `f`.
    *   `weights`: ndarray of barycentric weights, dtype matches input `z`.
    *   `errors`: ndarray of approximation errors per iteration, dtype matches `z.real`.
*   Implement methods on the returned object:
    *   `poles()`: Return a complex array of poles, dtype is `np.result_type(input_dtype, 1j)`.
    *   `residues()`: Return a complex array of residues at poles, dtype is `np.result_type(input_dtype, 1j)`.
    *   `roots()`: Return a complex array of roots, dtype is `np.result_type(input_dtype, 1j)`.
*   Ensure the algorithm handles `NaN` or `Inf` in function values by skipping those points.
*   Maintain scale-invariance: scaling function values should scale the approximation similarly.
*   Ensure numerically accurate results for poles, residues, and roots, consistent with the approximated function's properties.
*   When a smaller `rtol` is specified, ensure fewer support points are used.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.