Implement enhancements to the `top_k` and `bottom_k` methods to allow ranking based on multiple columns with configurable sort directions. Ensure these methods work within grouped aggregations and handle invalid inputs with descriptive errors.

*   Update the `top_k` and `bottom_k` methods in `py-polars/polars/expr/expr.py` to include:
    *   A new optional `by` parameter that accepts a string or list of strings specifying column names for ranking.
    *   A `descending` parameter that can be a boolean or list of booleans to control sort direction.
*   Ensure:
    *   When `by` is specified, `top_k` returns rows with the largest values and `bottom_k` returns rows with the smallest values in the `by` column(s), including all selected columns.
    *   If `by` is a list and `descending` is also a list, their lengths must match, or a `ValueError` with the message "the length of `descending` (N) does not match the length of `by` (M)" should be raised.
    *   If no `by` is provided and `descending` is a list, raise a `ValueError` with the message "`descending` should be a boolean if no `by` is provided".
    *   Both methods should function correctly within a `group_by().agg()` context, applying top/bottom selection per group.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.