I'm working with complex-valued numeric data in pandas and running into problems with dtype handling and arithmetic correctness.

*   When performing NumPy ufunc operations (such as sqrt or divide) or arithmetic operations (addition, subtraction, multiplication, division) on a Series or Index with complex128 dtype, the result dtype must remain complex128 rather than being coerced to float64. For integer and unsigned integer dtypes, the existing behavior of coercing to float64 must be preserved.

*   Floor division, reverse floor division, modulo, and reverse modulo operations on NumpyExtensionArray data with complex dtype must raise TypeError, consistent with the mathematical undefinedness of these operations for complex numbers.

*   The divmod operation on NumpyExtensionArray data with complex dtype (kind 'c') must raise TypeError.

*   NumpyExtensionArray must support complex dtype in duplicate detection (duplicated) operations, returning correct results for 'first', 'last', and False keep modes.

*   NumpyExtensionArray with complex dtype must raise an appropriate error when attempting to set an item using a scalar key with a sequence value (setitem with scalar key/sequence).

*   DataFrame arithmetic operators must work correctly on empty-like DataFrames with complex128 dtype, analogous to existing support for float and int64 dtypes.

*   NumPy ufuncs must be compatible with pandas Index, RangeIndex, and Series containers holding complex128 data.

*   For the fillna no-op-returns-copy behavior, complex dtype is an expected failure case due to the absence of a Cython backfill/pad implementation for complex dtype — this case should be marked accordingly rather than causing an unexpected error.

*   When unstacking a Series or DataFrame backed by a NumpyExtensionArray with complex dtype that requires NaN insertion, the fill value is (nan+0j) rather than scalar NaN; tests that rely on object-converted equality of these fill values should be marked as expected failures for complex dtype.

*   When using the C-engine CSV parser with complex dtype, the operation is an expected failure because the C parser does not support complex dtypes — this should be marked accordingly rather than causing an unexpected error.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.