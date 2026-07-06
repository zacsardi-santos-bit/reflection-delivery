## Description

DataFrame flexible arithmetic operations (like addition, subtraction, and multiplication) do not support the fill value parameter when the other operand is a Series or array. Attempting to pass a fill value alongside a Series raises a "not implemented" error, even though the same parameter is documented and works fine for other operand types.

## Expected Behavior

- Performing row-wise or column-wise arithmetic between a DataFrame and a Series while specifying a fill value for missing data should succeed and return correct results.
- When either the DataFrame or the Series contains NaN, the fill value should substitute for the missing entry before the operation is computed.
- Both row-aligned (index-based) and column-aligned cases should work.
- Index mismatches between the DataFrame and the Series should be handled correctly, expanding the result to cover all labels and applying the fill value where applicable.
- The same fill value support should work when multiplying a DataFrame by an array, including when the array uses extension array dtypes (integer or float).
- Zero-length DataFrame edge cases should also compute the correct result when a fill value is specified, rather than raising an error.

## Why This Matters

A common operation is to add or multiply a DataFrame by a row or column vector while treating missing values as a specific number (e.g., treat NaN as zero or as some default). Currently this fails entirely with a cryptic "not implemented" error. Fixing this allows users to compose Series-aligned arithmetic with missing-value handling in a single, clean expression.

## Additional Fix

When adding period-based datetime arrays to regular datetime arrays, the resulting error message should be the same regardless of whether the operands are wrapped in an Index, a Series, or a DataFrame. Previously the error message differed depending on the container type, making error handling harder to anticipate.
