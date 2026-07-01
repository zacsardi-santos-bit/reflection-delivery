Implement the necessary changes to improve the timestamp parsing system by addressing format validation, optional seconds handling, fallback year propagation, and file size type consistency.

*   Implement a `check_format` function in `application/apps/indexer/processor/src/parse.rs`:
    *   Accept a format string and return a `FormatCheckResult`.
    *   Return `FormatCheckResult::FormatRegex(String)` for valid formats containing year, month, day, hour, and minute.
    *   Return `FormatCheckResult::FormatInvalid(String)` if any required component is missing.
    *   Treat seconds and timezone as optional components.

*   Update the `extract_posix_timestamp` function in `application/apps/indexer/processor/src/parse.rs`:
    *   Ensure it does not fail if the seconds capture group is absent.
    *   Default the seconds value to 0 if missing.

*   Modify the `timespan_in_file` function in `application/apps/indexer/processor/src/parse.rs`:
    *   Add a third parameter `year: Option<i32>`.
    *   Use the `year` parameter as a fallback when the year is not present in log lines.

*   Modify the `scan_lines` function in `application/apps/indexer/processor/src/parse.rs`:
    *   Add a fifth parameter `year: Option<i32>`.
    *   Pass the `year` parameter to the timestamp extractor for lines missing a year component.

*   Update file size handling in the `create_index_and_mapping` function in `application/apps/indexer/processor/src/processor.rs`:
    *   Ensure the `source_file_size` parameter is of type `u64`.
    *   Allow direct passing of `fs::metadata().len()` without casting.

*   Ensure the `FormatCheckResult` enum is publicly exported from the `processor::parse` module.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.