Implement axis bounds checking for several tensor operations to ensure they raise a descriptive error when an out-of-range axis is provided. Update the specified operations to raise a `ValueError` with a clear message indicating the invalid axis value and that it is out of bounds.

*   Update the `unstack` function in `keras/src/ops/core.py`:
    *   Raise a `ValueError` with the message "axis N is out of bounds" if the axis is outside the valid range for the tensor's dimensions.

*   Update the following functions in `keras/src/ops/numpy.py`:
    *   `concatenate(xs, axis=0)`: Raise a `ValueError` with the message "axis N is out of bounds" if the axis is outside the valid range for the input tensors' dimensions.
    *   `split(x, indices_or_sections, axis=0)`: Raise a `ValueError` with the message "axis N is out of bounds" if the axis is outside the valid range for the input tensor's dimensions.
    *   `diff(a, n=1, axis=-1)`: Raise a `ValueError` with the message "axis N is out of bounds" if the axis is outside the valid range for the input tensor's dimensions.
    *   `take_along_axis(x, indices, axis=None)`: Raise a `ValueError` with the message "axis N is out of bounds" if the axis is outside the valid range for the input tensor's dimensions.
    *   `stack(xs, axis=0)`: Raise a `ValueError` with the message "axis N is out of bounds" if the axis is outside the valid range, considering the new axis added by stacking.

*   Ensure all error messages contain the literal text "axis N is out of bounds" with N being the invalid axis value provided.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.