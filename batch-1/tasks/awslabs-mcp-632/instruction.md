Implement a proactive SQL security layer in the Aurora DSQL MCP server to validate queries before establishing a database connection. Detect and reject potentially harmful SQL statements, including write-modifying statements, SQL injection patterns, and transaction bypass attempts, with clear error messages.

*   Create a new module at `awslabs/aurora_dsql_mcp_server/mutable_sql_detector.py` with the following functions:
    *   `detect_mutating_keywords(sql: str) -> list[str]`: 
        *   Return an empty list for empty strings, whitespace-only SQL, and comment-only SQL.
        *   Return an empty list for safe SELECT queries.
        *   Return 'DDL' for statements like CREATE TABLE, DROP TABLE, etc., and include 'CREATE' and 'DROP' as individual entries.
        *   Return 'PERMISSION' for GRANT, REVOKE, CREATE USER, DROP USER, and include individual keywords.
        *   Return 'SYSTEM' for operations like SET GLOBAL, FLUSH PRIVILEGES, etc.
        *   Return 'TRANSACTION_CONTROL' for transaction control statements.
        *   Return individual keywords for 'INSERT', 'UPDATE', 'DELETE', etc. Detection must be case-insensitive and deduplicated.
    *   `check_sql_injection_risk(sql: str) -> list[dict]`:
        *   Return an empty list for empty strings, whitespace-only SQL, comment-only SQL, and safe SELECT queries.
        *   Return a non-empty list for patterns like OR-tautologies, UNION SELECT attacks, etc.
        *   Stop at the first matched pattern and return one issue dict with keys: 'type' ('sql'), 'message' ('Suspicious pattern detected'), and 'severity' ('high').
    *   `detect_transaction_bypass_attempt(sql: str) -> bool`:
        *   Return False for empty strings, whitespace-only SQL, comment-only SQL, and single statements with no trailing semicolons.
        *   Return True for multiple SQL statements separated by semicolons.
        *   Return False when a semicolon is followed only by a comment or whitespace. Detection must be case-insensitive.

*   Add the following string constants to `awslabs/aurora_dsql_mcp_server/consts.py`:
    *   `ERROR_WRITE_QUERY_PROHIBITED`
    *   `ERROR_QUERY_INJECTION_RISK`
    *   `ERROR_TRANSACTION_BYPASS_ATTEMPT`

*   Update the `readonly_query` function in `server.py`:
    *   Perform security validation before any database connection.
    *   Call `detect_mutating_keywords` first; if non-empty, raise an Exception with `ERROR_WRITE_QUERY_PROHIBITED`.
    *   Call `check_sql_injection_risk`; if non-empty, raise an Exception with `ERROR_QUERY_INJECTION_RISK`.
    *   Call `detect_transaction_bypass_attempt`; if True, raise an Exception with `ERROR_TRANSACTION_BYPASS_ATTEMPT`.
    *   Ensure `get_connection` and `execute_query` are not called when a query is blocked.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.