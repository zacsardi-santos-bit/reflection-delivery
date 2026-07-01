Update the SQL logic test runner to execute from the project root directory, and adjust all SQL test scripts to use root-relative paths. Ensure that all test functionalities continue to operate correctly with these path changes.

*   Modify the SQL logic test runner to execute from the project root directory.
    *   Ensure that relative data file paths in SQL test scripts resolve against the project root.
*   Update all SQL logic test files to use root-relative paths:
    *   Change paths from '../../testdata/...' to './testdata/...'.
*   Adjust the module at `crates/testing/src/slt/tests.rs`:
    *   Import `FnTest` and `TestClient` using `use super::test::{FnTest, TestClient}`.
*   Ensure scan functions operate correctly with root-relative paths:
    *   Functions include `parquet_scan`, `read_parquet`, `csv_scan`, `read_csv`, `ndjson_scan`, `read_ndjson`, `delta_scan`, `lance_scan`.
    *   Example path: './testdata/parquet/userdata1.parquet'.
*   Verify direct file-path FROM clauses work with root-relative paths:
    *   Ensure correct operation for parquet, CSV, and JSON files.
    *   Example query: `SELECT ... FROM './testdata/parquet/userdata1.parquet'`.
*   Ensure glob patterns with root-relative paths function correctly:
    *   Example pattern: './testdata/parquet/*.parquet'.
    *   Verify correct matching and combined row count.
*   Validate file type inference with root-relative paths:
    *   Return 1000 rows for known file types (parquet/CSV/JSON).
    *   Return errors for unknown file extensions ('unable to infer') and missing extensions ('missing file extension').
*   Ensure CREATE operations succeed with root-relative paths:
    *   Operations include `CREATE TABLE AS SELECT`, `CREATE VIEW AS SELECT`, and `INSERT INTO SELECT FROM`.
*   Validate CREATE EXTERNAL TABLE operations:
    *   Succeed with a location option set to a root-relative path without a tunnel.
    *   Fail with a tunnel-related error when using a tunnel, but succeed without it.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.