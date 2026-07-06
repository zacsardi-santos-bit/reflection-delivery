Update the `sph_harm_y` function in `scipy/special/_multiufuncs.py` to ensure the derivative dimensions are trailing in the output arrays. This will align the function's output with standard array conventions, improving usability for batch computations.

*   Modify `sph_harm_y` to return a 2-tuple (y, y_jac) when `diff_n=1`:
    *   Ensure `y_jac` has the derivative dimension as the last axis with shape `(..., 2)`.
    *   Allow indexing with `y_jac[..., 0]` for the theta-component and `y_jac[..., 1]` for the phi-component.

*   Modify `sph_harm_y` to return a 3-tuple (y, y_jac, y_hess) when `diff_n=2`:
    *   Ensure `y_jac` has shape `(..., 2)` and `y_hess` has shape `(..., 2, 2)`.
    *   Place both derivative dimensions as the last two axes in `y_hess`.

*   Implement the following mathematical requirements:
    *   `y_jac[..., 0]` must equal the theta-derivative of the associated Legendre polynomial multiplied by `exp(1j * m * phi)`.
    *   `y_jac[..., 1]` must equal `1j * m * p * exp(1j * m * phi)`.
    *   `y_hess[..., 0, 0]` must equal the second theta-derivative of the associated Legendre polynomial multiplied by `exp(1j * m * phi)`.
    *   `y_hess[..., 0, 1]` and `y_hess[..., 1, 0]` must be equal and equal to `1j * m * p_jac * exp(1j * m * phi)`.
    *   `y_hess[..., 1, 1]` must equal `-m^2 * p * exp(1j * m * phi)`.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.