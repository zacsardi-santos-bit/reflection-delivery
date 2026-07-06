I'm working with sparse arrays in pandas and noticed that the mean reduction doesn't properly respect the option to include or propagate missing values.

*   The `mean` method of SparseArray must accept a `skipna` boolean keyword argument with a default value of True.

*   When `mean(skipna=True)` is called on a float SparseArray containing NaN values, it must return the mean of the non-NaN values (same result as calling `mean()` with no arguments).

*   When `mean(skipna=False)` is called on a float SparseArray containing any NaN values, it must return a missing (NA) value, as determined by `isna()` returning True on the result.

*   When `mean(skipna=True)` is called on a SparseArray with NaN fill_value (e.g., `SparseArray([1.0, np.nan, 3.0], fill_value=np.nan)`), it must return the mean of the non-NaN values (e.g., 2.0).

*   When `mean(skipna=False)` is called on a SparseArray with NaN fill_value that contains NaN values, it must return a missing (NA) value.

*   When `mean(skipna=True)` or `mean(skipna=False)` is called on an object-dtype SparseArray containing NaN values alongside non-numeric elements, a TypeError must be raised with a message matching 'unsupported operand type'.

*   The `mean` method must perform arithmetic operations before applying any skipna-based NA short-circuit, so that dtype-level errors (such as TypeError for unsupported types) are raised regardless of the skipna value.

*   The `mean` reduction on float-backed SparseArray with `skipna=False` must no longer require any special skip or xfail treatment in the extension array test suite — it must return NA (not raise or produce wrong results) when the array contains NaN values.


*   Interface details: Type: Method
Name: mean
Location: pandas/core/arrays/sparse/array.py
Signature: mean(self, axis: Axis = 0, *args, skipna: bool = True, **kwargs) -> float | NAType
Description: Computes the mean of non-NA/null values in a SparseArray. Accepts a `skipna` boolean keyword argument (default True). When `skipna=True`, NA/null values are excluded from the computation. When `skipna=False` and any NA/null values are present, the method returns NA instead of a numeric result. For object-dtype arrays where arithmetic is not supported, raises TypeError regardless of the `skipna` value.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.