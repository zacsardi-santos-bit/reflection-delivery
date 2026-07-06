Implement enhancements to the SQL query optimizer to ensure that single-row lookup optimization applies to queries with table aliases, and that alias-qualified column references are correctly resolved. Ensure that invalid references using the original table name or unknown qualifiers produce appropriate errors.

*   Apply point get optimization to single-row lookup queries using a table alias.
    *   Ensure the optimizer does not skip the point get plan due to the presence of a table alias.
*   Resolve column references correctly when a table alias is used:
    *   In the WHERE clause, resolve alias-qualified column references to allow the point get plan.
    *   In the SELECT list, resolve alias-qualified column references, including wildcards.
*   Handle queries selecting both wildcards and specific columns:
    *   Ensure all expected columns are returned in the correct order through the point get path.
*   Produce errors for invalid references when a table alias is provided:
    *   If the original table name is used in the WHERE clause, return: "Unknown column '<original_table>.<col>' in 'where clause'".
    *   If the original table name is used in the SELECT list, return: "Unknown column '<original_table>.<col>' in 'field list'".
    *   If the original table name is used with a wildcard, return: "Unknown table '<original_table>'".
*   Produce errors for non-existent table qualifiers:
    *   In the WHERE clause, return: "Unknown column 'xxxxx.<col>' in 'where clause'".
    *   In the SELECT list, return: "Unknown column 'xxxxx.<col>' in 'field list'".
*   Ensure point get optimization works correctly for CHAR and CHAR BINARY primary key columns:
    *   Include scenarios with and without PAD_CHAR_TO_FULL_LENGTH SQL mode.
*   Ensure point get optimization works correctly for BINARY primary key columns:
    *   Verify that PAD_CHAR_TO_FULL_LENGTH mode does not affect binary comparison behavior.
*   Ensure index lookup queries on CHAR and BINARY indexed columns work correctly:
    *   Apply to both primary and non-primary-key columns, with and without table aliases.
    *   Include scenarios with PAD_CHAR_TO_FULL_LENGTH SQL mode.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.