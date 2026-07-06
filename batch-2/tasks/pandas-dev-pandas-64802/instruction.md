I'm working with 2D nullable integer arrays and running into issues with axis-based reductions.

*   BaseMaskedArray.min(axis=0) and BaseMaskedArray.max(axis=0) on a 2D masked array must return a 1D BaseMaskedArray of the same numeric dtype, computing the column-wise minimum/maximum while skipping missing values (skipna=True by default).

*   BaseMaskedArray.min(axis=1) and BaseMaskedArray.max(axis=1) on a 2D masked array must return a 1D BaseMaskedArray of the same numeric dtype, computing the row-wise minimum/maximum while skipping missing values.

*   When a column (axis=0) or row (axis=1) consists entirely of missing values, min and max must return a missing value (pd.NA) at the corresponding position in the result array.

*   BaseMaskedArray.min(axis=None) and BaseMaskedArray.max(axis=None) must return a scalar equal to the result on the raveled (flattened) 1D array.

*   BaseMaskedArray.mean(axis=0) on a 2D masked array must return a 1D FloatingArray. When a column is entirely missing values, that position in the result must be pd.NA rather than aborting with a scalar NA early.

*   BaseMaskedArray.sum/prod/mean/var/std called with axis=0 and skipna=False on a 2D masked array must return a BaseMaskedArray where positions corresponding to columns that had any missing value are NA, and positions with no missing values hold the valid computed result.

*   BaseMaskedArray.min/max called with axis=0 and skipna=False on a 2D masked array must return a BaseMaskedArray where positions corresponding to columns that contained any missing value are NA, and positions without missing values hold the valid computed result.

*   BaseMaskedArray.sum(axis=0, min_count=1) must return NA at positions where all column values are missing, and valid sums elsewhere.

*   BaseMaskedArray.var(axis=0, ddof=0) and BaseMaskedArray.std(axis=0, ddof=0) must return 1D FloatingArray with correct column-wise variance and standard deviation, skipping missing values.


*   Interface details: Type: Class
Name: BaseMaskedArray
Location: pandas/core/arrays/masked.py
Description: Base class for nullable masked arrays. The following reduction methods must support a 2D array with axis=0 or axis=1, returning a 1D BaseMaskedArray result with correct NA handling, in addition to the existing axis=None scalar behavior.
Signature: min(axis=None, skipna=True, **kwargs) -> scalar | BaseMaskedArray
Signature: max(axis=None, skipna=True, **kwargs) -> scalar | BaseMaskedArray
Signature: sum(axis=None, skipna=True, min_count=0, **kwargs) -> scalar | BaseMaskedArray
Signature: prod(axis=None, skipna=True, min_count=0, **kwargs) -> scalar | BaseMaskedArray
Signature: mean(axis=None, skipna=True, **kwargs) -> scalar | BaseMaskedArray
Signature: var(axis=None, skipna=True, ddof=1, **kwargs) -> scalar | BaseMaskedArray
Signature: std(axis=None, skipna=True, ddof=1, **kwargs) -> scalar | BaseMaskedArray

Type: Class
Name: IntegerArray
Location: pandas/core/arrays/integer.py
Description: Subclass of BaseMaskedArray for nullable integer data. Inherits all reduction methods listed above. The class method _simple_new(data, mask) constructs an instance from a raw numpy array and boolean mask.

Type: Class
Name: FloatingArray
Location: pandas/core/arrays/floating.py
Description: Subclass of BaseMaskedArray for nullable floating-point data. Inherits all reduction methods listed above. The class method _simple_new(data, mask) constructs an instance from a raw numpy array and boolean mask.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.