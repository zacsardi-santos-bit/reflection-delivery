## Description

When scanning Parquet files and filtering on floating-point columns, the query engine attempts to skip entire row groups that cannot satisfy the filter condition by examining the min/max statistics stored in the file. However, Parquet row-group statistics exclude NaN values — yet the actual data may contain NaN. This creates a correctness problem: for certain comparisons like "greater than", a row group containing only NaN values would be incorrectly skipped, causing those rows to disappear from query results even though they should be returned or at minimum not silently dropped.

## Expected Behavior

The batch-skipping optimization for floating-point columns should only be applied when it is provably safe:

- "Less than" and "less than or equal" comparisons with any bound (including NaN) are safe, because NaN never satisfies such comparisons.
- Equality comparisons with a non-NaN literal are safe, because NaN is never equal to any non-NaN value.
- Equality and inequality comparisons with a NaN literal are not safe, because statistics do not capture NaN.
- "Greater than" and "greater than or equal" comparisons with non-NaN literals are not safe, because hidden NaN values in a row group would satisfy them even though statistics do not reflect this.
- "Greater than or equal to NaN" and "greater than NaN" are safe, because nothing can be greater than NaN under total ordering.
- Range checks are safe only when both bounds are non-NaN constants.
- When the comparison is written with the column on the right side, the operator direction must be accounted for when determining safety.

## Why This Matters

This bug could cause floating-point data to be silently dropped from query results when filters like "x > 5.0" are applied to Parquet files containing NaN values in column x. Fixing it ensures correctness while still allowing safe optimizations to proceed.
