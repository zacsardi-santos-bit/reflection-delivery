I've noticed that skewness and kurtosis calculations give inconsistent results when the data has zero or near-zero variance.

*   When a series, DataFrame column, rolling window, or grouped aggregation contains all equal (constant) values, computing skewness or kurtosis must return NaN rather than a numeric value such as 0.0 or -3.0, because skewness and kurtosis are mathematically undefined for a zero-variance (degenerate) distribution.

*   The nanskew function must return NaN (not 0.0) for any input array where all finite, non-NaN values are equal (constant series), regardless of the length of the array.

*   The nankurt function must return NaN (not 0.0) for any input array where all finite, non-NaN values are equal (constant series), regardless of the length of the array.

*   Series.skew() must return NaN for any Series of all-equal values, including Series of length 3 or more (which previously returned 0.0). The return type must be float when using Python scalars, and np.float64 otherwise.

*   Series.kurt() must return NaN for any Series of all-equal values, including Series of length 4 or more (which previously returned 0.0). The return type must be float when using Python scalars, and np.float64 otherwise.

*   DataFrame.skew() and DataFrame.kurt() must return all-NaN for DataFrames composed entirely of equal values.

*   Rolling window skew() must return NaN (not 0.0) when all values within the current window are equal, regardless of the window size or step.

*   Rolling window kurt() must return NaN (not -3.0) when all values within the current window are equal, regardless of the window size or step.

*   Groupby kurt() with skipna=False must return NaN (not 0.0) for groups where all values are equal.

*   Skewness and kurtosis results must be consistent across all computation paths (plain Series reduction, groupby aggregation, rolling window, and DataFrame reduction) when applied to the same data — this must hold for constant distributions (zero variance), near-zero variance distributions (scale ~1e-18), and large-scale distributions (scale ~1e18).

*   Rolling skew() and rolling kurt() must be scale-invariant: multiplying all input values by a constant factor (e.g. 1e-20 or 1e20) must not change the result.

*   The mask parameter of nanskew and nankurt must accept a numpy boolean array (not a pandas Series).


*   Interface details: Type: Function
Name: nanskew
Location: pandas/core/nanops.py
Signature: nanskew(values, axis=None, skipna=True, mask=None) -> float | np.ndarray
Description: Computes skewness while skipping NaN values. For arrays where all (non-NaN) values are equal (zero variance), must return np.nan. The mask parameter must accept a numpy boolean array.

Type: Function
Name: nankurt
Location: pandas/core/nanops.py
Signature: nankurt(values, axis=None, skipna=True, mask=None) -> float | np.ndarray
Description: Computes excess kurtosis while skipping NaN values. For arrays where all (non-NaN) values are equal (zero variance), must return np.nan. The mask parameter must accept a numpy boolean array.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.