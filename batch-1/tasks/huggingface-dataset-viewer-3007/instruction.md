Implement support for datetime and timestamp columns in the dataset viewer statistics feature. Add a new column type for datetime data to compute and return specific statistics, and enhance string column handling to detect and process datetime values when applicable.

*   Implement a new `DatetimeColumn` class in `services/worker/src/worker/statistics_utils.py`.
    *   Ensure it is importable from `worker.statistics_utils`.
    *   Implement `compute_statistics(data: pl.DataFrame, column_name: str, n_samples: int) -> dict` to return a dictionary with keys: `nan_count`, `nan_proportion`, `min`, `max`, `mean`, `median`, `std`, and `histogram`.
        *   Return `None` for all statistical fields if all values are null.
        *   For non-null columns, return `min`, `max`, `mean`, `median` as datetime strings with second precision, and `std` as a time duration string.
        *   Include timezone offsets in datetime strings for timezone-aware columns.
        *   Use string representations for histogram bin edges.
    *   Implement `compute_and_prepare_response(data: pl.DataFrame) -> StatisticsPerColumnItem` to return a `StatisticsPerColumnItem` with `column_type` set to `ColumnType.DATETIME`.

*   Update the `ColumnType` enum in `services/worker/src/worker/statistics_utils.py` to include a new value `ColumnType.DATETIME`.

*   Modify `StringColumn` class:
    *   Add a class-level boolean attribute `ENABLE_DATETIME` defaulting to `False`.
    *   When `ENABLE_DATETIME` is `True`, modify `StringColumn.compute_statistics()` to:
        *   Detect and process columns with consistently formatted datetime values using `DatetimeColumn.compute_statistics()`.
        *   Fall back to standard string statistics for columns with mixed or unrecognizable datetime formats.

*   Ensure the descriptive statistics job runner supports datasets with timestamp columns by routing them to `DatetimeColumn` for statistics computation.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.