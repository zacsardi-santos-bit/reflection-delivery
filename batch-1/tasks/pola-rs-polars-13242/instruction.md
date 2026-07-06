Implement support for multi-column joins in the Polars dataframe library's SQL interface. Ensure that the SQL engine can handle join conditions combining multiple equality comparisons with AND for both inner and left joins. Maintain error handling for malformed conditions.

*   Enable JOIN ON clauses to support multiple equality conditions combined with AND.
    *   Ensure INNER JOINs return rows where all specified conditions are satisfied.
    *   Ensure LEFT JOINs preserve all rows from the left table, returning NULL for unmatched columns from the right table.
*   Support cross-table column matching where matched columns have different names.
*   Allow three or more equality conditions chained with AND in a single JOIN.
*   Ensure multiple sequential JOINs with compound conditions are correctly applied.
*   Support compound join ON clauses within subqueries that are joined with other tables.
*   Maintain error handling for malformed JOIN ON clauses:
    *   Reject clauses where column references are not fully table-qualified.
    *   Reject clauses where sub-expressions are not equality comparisons.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.