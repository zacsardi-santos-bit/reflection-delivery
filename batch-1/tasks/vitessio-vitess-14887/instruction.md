Update the SQL error parsing logic to correctly identify and extract the first occurrence of an errno/sqlstate pattern from an error message, ensuring accurate error classification. Implement the following requirements:

*   Modify the `NewSQLErrorFromError` function in `go/mysql/sqlerror/sql_error.go`:
    *   Ensure it extracts the first occurrence of an errno/sqlstate pattern from an error message, even if multiple patterns exist.
    *   When encountering an error message with patterns like '(errno 1062) (sqlstate 23000)' followed by another pattern such as '(errno 1366) (sqlstate 10000)', ensure it returns a `*SQLError` with `Number()` equal to `ERDupEntry` (1062) and `SQLState()` equal to `SSConstraintViolation` ("23000").
    *   Ensure the returned value satisfies `errors.As` for `*SQLError`, allowing callers to unwrap the result to a `*SQLError`.

*   Adjust the `errExtract` regular expression in `go/mysql/sqlerror/sql_error.go`:
    *   Ensure it matches the first (leftmost) occurrence of the pattern '(errno N) (sqlstate S)' in the input string.
    *   Avoid using leading/trailing wildcard anchors that might cause the regex engine to prefer a later match over an earlier one.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.