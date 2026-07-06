Implement tolerance support for the "nearest" matching strategy in the `join_asof` method for both DataFrame and LazyFrame. Fix the bug where the last right element is incorrectly matched to multiple consecutive left rows. Ensure the `tolerance` parameter accepts `datetime.timedelta` objects, and apply tolerance constraints correctly in grouped joins.

*   Update the `join_asof` method in `py-polars/polars/dataframe/frame.py` and `py-polars/polars/lazyframe/frame.py`:
    *   Modify the `tolerance` parameter to accept `str | int | float | timedelta | None`.
    *   Convert `timedelta` objects to equivalent string durations before processing.
*   Implement tolerance logic for `strategy='nearest'`:
    *   For numeric keys, only match right rows within the specified numeric `tolerance`.
    *   For datetime keys, support both string duration and `timedelta` as `tolerance`.
    *   Ensure no match is made if no right key is within the specified tolerance, resulting in null.
    *   Handle exact matches when `tolerance` is 0, allowing only zero-distance matches.
    *   Apply tolerance constraints independently per group when using the `by` parameter.
*   Fix the bug in the nearest strategy:
    *   Ensure the last right element correctly matches multiple consecutive left rows.
    *   Reset the distance state for each left row to prevent incorrect matches.
*   Ensure that when using `strategy='nearest'` with `by` and no tolerance, the last item of the right group matches correctly for all applicable left rows.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.