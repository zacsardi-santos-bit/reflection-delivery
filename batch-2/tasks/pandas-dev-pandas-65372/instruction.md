I'm seeing inconsistent behavior when computing skewness and kurtosis on grouped nullable floating-point data.

*   When nanops reduction functions (nansum, nanmean, nanstd, nanvar, nansem, nanmax, nanmin, nanskew, nankurt, nanprod) are called with a boolean `mask` parameter, the mask is the sole indicator of NA-ness: entries where mask=True are treated as NA (missing), and entries where mask=False are treated as valid data regardless of whether their value is NaN.

*   When nanskew or nankurt is called with a mask and skipna=True: only mask=True entries are skipped; any entry with mask=False and a NaN value is treated as a valid (non-NA) number and participates in computation, causing NaN to propagate arithmetically to the result.

*   When nanskew or nankurt is called with a mask and skipna=False: any mask=True entry encountered during computation must cause the result to propagate as NaN (i.e., the NA entry is not skipped and causes the result to be NaN).

*   nanskew and nankurt must produce correct NaN output for axis=None (scalar), axis=0, and axis=1 when the data contains both NaN values (mask=False) and masked NA entries (mask=True).

*   The GroupBy skew and kurt reduction methods, when applied to a FloatingArray-based Series where the underlying values contain NaN but the mask is all False, must return a result of pd.NA (when the NaN-vs-NA distinction option is active) or np.nan (when it is not active), with dtype Float64.

*   When the `future.distinguish_nan_and_na` option is set to True and skipna=False, groupby reductions (sum, mean, std, var, sem, skew, kurt, prod) must return pd.NA in the result for groups that contain any pd.NA value, with result dtype Float64.


*   Interface details: Type: Function
Name: nanskew
Location: pandas/core/nanops.py
Signature: nanskew(values, axis=None, skipna=True, mask=None, ...) -> np.ndarray | float
Description: Computes skewness, ignoring or propagating NA values according to skipna. When mask is provided, mask=True entries are the sole indicator of NA. With skipna=True and mask provided, only mask=True entries are skipped; NaN values at mask=False positions propagate NaN arithmetically to the result. With skipna=False and mask provided, any mask=True entry causes the result to be NaN.

Type: Function
Name: nankurt
Location: pandas/core/nanops.py
Signature: nankurt(values, axis=None, skipna=True, mask=None, ...) -> np.ndarray | float
Description: Computes excess kurtosis, ignoring or propagating NA values according to skipna. When mask is provided, mask=True entries are the sole indicator of NA. With skipna=True and mask provided, only mask=True entries are skipped; NaN values at mask=False positions propagate NaN arithmetically to the result. With skipna=False and mask provided, any mask=True entry causes the result to be NaN.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.