Implement support for converting second-precision timestamps to human-readable date/time representations in the `from_datafusion` function. Ensure that second-level precision timestamps are correctly handled, similar to microsecond and nanosecond precision timestamps.

Requirements:
*   Update the `from_datafusion` function in `crates/pgrepr/src/scalar.rs` to handle second-precision timestamps.
    *   Ensure the function signature remains `from_datafusion(value: DfScalar, as_type: &PgType) -> Scalar`.
    *   When the input is a `DfScalar::TimestampSecond(Some(v), None)`, convert it to `Scalar::Timestamp`.
        *   Use `NaiveDateTime::from_timestamp_opt(v, 0).unwrap()` to create the naive datetime.
        *   The `v` is an `i64` representing seconds since the UNIX epoch.
*   Specifically, ensure that when `from_datafusion` receives a second-precision timestamp value of 938,709,124 with no timezone, it returns a `Scalar::Timestamp` equal to the naive datetime constructed via `NaiveDateTime::from_timestamp_opt(938_709_124, 0).unwrap()`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.