## Description

Skewness and kurtosis calculations produce inconsistent and mathematically incorrect results for constant or near-constant data, depending on which computation path is used.

A constant distribution — where all values are identical — has a variance of zero, making skewness and kurtosis mathematically undefined. The correct result in this case is NaN (not a number). However, currently a numeric value (such as zero for skewness and negative three for kurtosis) is returned in some code paths when all values in a window or group are equal, while NaN is returned in others. This inconsistency varies by whether you compute using a plain data column, a rolling window, a grouped aggregation, or a column in a multi-column table.

Additionally, when data has very small or very large magnitudes, floating-point precision issues cause skewness and kurtosis to disagree across computation paths, even for non-degenerate distributions.

## Expected Behavior

- Computing skewness or kurtosis on a constant series (all equal values) should always return NaN, regardless of series length and regardless of which API is used.
- All computation paths (direct reduction on individual columns or multi-column tables, grouped aggregation, rolling window) should return consistent values for the same underlying data.
- The results should be numerically stable and scale-invariant: multiplying the data by a very small or very large constant should not change the skewness or kurtosis.

## Why This Matters

Users relying on skewness and kurtosis for statistical analysis may get silently incorrect or inconsistent results depending on how they invoke these operations. The behavior should be well-defined and consistent: a zero-variance distribution should always yield NaN for these higher-order moments.
