## Description

Reduction operations (minimum, maximum, mean, and aggregations with missing-value propagation) on 2D nullable integer and floating-point arrays do not work correctly when reducing along a specific axis.

Specifically:

- Finding the minimum or maximum value along either axis of a 2D nullable array raises an error or returns incorrect results instead of computing the per-column or per-row extremes while properly skipping missing values.
- When an entire column consists of missing values, the minimum or maximum for that column should be a missing value in the output — but this does not happen correctly.
- Computing the mean along an axis when a column is entirely missing should yield a missing value at that column's position in a result array — instead the operation short-circuits and returns a scalar missing value for the whole result.
- When missing-value propagation is requested (i.e., the user does not want missing values skipped), aggregations along an axis should produce missing values at positions where any input was missing. This also does not work correctly for any of the supported aggregation functions.

## Expected Behavior

- Column-wise and row-wise minimum/maximum on 2D nullable integer or float arrays should return a 1D nullable array of the same type with missing values at positions where all inputs were missing.
- Mean along an axis when an entire column is missing should return a missing value at that column's position, not abort entirely.
- All aggregations (sum, product, mean, variance, standard deviation, minimum, maximum) called with missing-value propagation along an axis should return a 1D nullable array with missing values wherever any input slice contained a missing value.

## Why This Matters

Users who work with 2D nullable integer or float arrays — for example, those arising from internal DataFrame operations — need reliable axis-based reductions. Broken min/max and incorrect propagation of missing values make it impossible to correctly aggregate such arrays.
