Implement the correct compilation of interval and duration arithmetic for PRQL queries targeting Snowflake. Ensure the generated SQL adheres to Snowflake's specific interval expression requirements.

*   Render interval expressions in Snowflake SQL with both the numeric value and unit name enclosed in a single-quoted string.
    *   Example: Use `INTERVAL '10 DAY'` for adding 10 days.
*   Ensure the interval unit is in uppercase singular form.
    *   Use "DAY" instead of "DAYS" or "day".
*   Include derived columns in the SELECT clause with an alias when adding a duration to a column.
    *   Maintain a wildcard for existing columns.
*   Double-quote all column and table identifiers in the Snowflake SQL output.
*   Ensure the PRQL compiler can compile queries with interval/duration arithmetic without errors when targeting Snowflake.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.