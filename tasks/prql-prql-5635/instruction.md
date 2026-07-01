Implement a fix in the PRQL compiler to ensure that when a group expression with no partition columns is used, any specified sort order is preserved in the generated SQL. This will prevent the silent omission of the ORDER BY clause, especially when a row-limiting operation is also present.

*   Ensure that the PRQL compiler:
    *   Preserves the sort order in the generated SQL when an empty group expression (no partition columns) is used.
    *   Includes an ORDER BY clause in the SQL output for queries with an inner sort and take transform.
*   Verify that the compiled SQL for a query using 'from foo' with an empty group containing 'sort a' and 'take 1' produces:
    *   `SELECT * FROM foo ORDER BY a LIMIT 1`
*   Treat an empty group (no partition keys) as a full-table operation:
    *   Ensure sort orders specified inside are not discarded during compilation.
*   Update the compile function to handle such queries without returning errors.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.