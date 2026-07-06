Implement validation for axis parameters in specified tensor operations to ensure they are within bounds during symbolic graph construction. Raise a descriptive error if an axis is out of range.

*   Update the `softmax` function in `keras/src/ops/nn.py`:
    *   Validate the `axis` parameter during symbolic/static shape computation.
    *   Raise a `ValueError` with the message 'axis {N} is out of bounds' if the axis is out of range for the tensor's dimensions.

*   Update the `normalize` function in `keras/src/ops/nn.py`:
    *   Validate the `axis` parameter during symbolic/static shape computation.
    *   Raise a `ValueError` with the message 'axis {N} is out of bounds' if the axis is out of range for the tensor's dimensions.

*   Update the `swapaxes` function in `keras/src/ops/numpy.py`:
    *   Validate both `axis1` and `axis2` parameters during symbolic/static shape computation.
    *   Raise a `ValueError` with the message 'axis {N} is out of bounds' if either axis is out of range for the tensor's dimensions.

*   Update the `moveaxis` function in `keras/src/ops/numpy.py`:
    *   Validate `source` and `destination` axis values during symbolic/static shape computation.
    *   Raise a `ValueError` with the message 'axis {N} is out of bounds' if any axis is out of range for the tensor's dimensions.

*   Ensure the error message format is consistent: 'axis {N} is out of bounds', where {N} is the out-of-range axis value.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.