## Description

Daft currently supports grouping a dataframe and applying standard aggregation functions (sum, count, min, max, etc.) to each group. However, there is no way to apply an arbitrary user-defined function to an entire group of rows at once — receiving all the group's rows as a batch and returning any number of output rows. This makes it impossible to compute custom per-group statistics or transformations that require seeing the full group at once.

## Expected Behavior

- After grouping a dataframe, users should be able to call a method that accepts a user-defined function and applies it independently to each group.
- The output should be a new dataframe containing the group key columns plus a result column produced by the user-defined function.
- The name of the result column should default to the name of the first input expression passed to the user-defined function.
- If the user-defined function returns more than one row for a group, the group key values should be repeated for each output row (broadcast to match the output length).
- The operation should work with single-key and multi-key groupby operations.
- The operation should accept compound expressions as inputs to the user-defined function, not just simple column references.

## Why This Matters

This capability is essential for applying custom analytics, window-style computations, or statistical functions (standard deviation, custom normalization, etc.) per group — things that aren't possible with the existing built-in aggregation functions. Users familiar with similar "apply" or "transform" patterns in other dataframe libraries would expect this kind of flexibility.
