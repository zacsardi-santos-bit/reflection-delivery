Implement the necessary changes to ensure Beyla correctly identifies and processes SQL queries for PostgreSQL connections, even when table names cannot be extracted. Update the SQL validation logic to account for the database context and enhance the PostgreSQL query parser to handle double-quoted identifiers.

*   Modify the `validSQL` function in `pkg/internal/ebpf/common/sql_detect_transform.go`:
    *   Add a third parameter of type `request.SQLKind` to the function signature.
    *   Ensure the function returns `false` when called with `request.DBGeneric` if either the `op` or `table` strings are empty.
    *   Ensure the function returns `true` when called with `request.DBPostgres` if the `op` string is non-empty, regardless of the `table` string's content.

*   Update all existing calls to `validSQL`:
    *   In `pkg/internal/ebpf/common/sql_detect_transform.go`, update `detectSQLPayload` to pass the detected database kind as the third argument.
    *   In `pkg/internal/ebpf/common/tcp_detect_transform.go`, update `ReadTCPRequestIntoSpan` to pass the detected database kind as the third argument.

*   Enhance the PostgreSQL query parser:
    *   Ensure it correctly handles SELECT queries with double-quoted identifiers for table and column names.
    *   Extract the SQL operation (e.g., 'SELECT') and a comma-separated list of table names from the FROM clause and any JOIN clauses.
    *   Ensure the extracted table names are correctly joined as a string (e.g., 'auth_permission,auth_user_user_permissions').

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.