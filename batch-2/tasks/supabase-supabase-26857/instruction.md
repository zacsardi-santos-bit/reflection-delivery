Implement utility functions to manage SQL query limits in the SQL editor. Ensure that simple SELECT queries have an automatic row limit appended when appropriate, and format the SQL string correctly with the limit applied.

*   Implement the `checkIfAppendLimitRequired` function:
    *   Accepts a SQL string and a numeric limit.
    *   Returns an object with `appendAutoLimit` as a boolean.
    *   Return `appendAutoLimit: false` if:
        *   The limit is 0 or negative.
        *   The SQL contains the LIMIT keyword.
        *   The SQL is not a SELECT statement.
        *   The SQL contains multiple statements.
        *   The SQL contains line comments (`--`).
    *   Return `appendAutoLimit: true` if:
        *   The limit is positive.
        *   The SQL is a single SELECT statement.
        *   The SQL does not contain the LIMIT keyword or comments.
        *   There is only one statement.

*   Implement the `suffixWithLimit` function:
    *   Accepts a SQL string and a numeric limit.
    *   Returns a SQL string with:
        *   All trailing semicolons removed.
        *   Appends ' limit {n};' where {n} is the limit value.
    *   Ensures the output ends with exactly one semicolon, regardless of the original semicolon count.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.