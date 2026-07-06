Implement a fix in the PRQL compiler to ensure that when targeting Microsoft SQL Server, any SQL generated with DISTINCT and pagination uses a valid ORDER BY clause. Ensure that the ORDER BY clause references a column from the SELECT list rather than a placeholder expression.

*   Modify the PRQL compiler to adjust the ORDER BY clause when compiling for MSSQL with DISTINCT and OFFSET/FETCH pagination.
    *   Ensure the ORDER BY clause references the first column from the SELECT list if no explicit sort order is provided.
    *   If the SELECT list contains only wildcards, use a placeholder expression as a fallback.
*   Translate row-count limits to MSSQL's OFFSET/FETCH pagination syntax:
    *   Use "OFFSET 0 ROWS FETCH FIRST N ROWS ONLY" for limiting results.
*   Ensure all column names in the SELECT list and ORDER BY clause are double-quoted.
*   Validate that the generated SQL is executable on MSSQL without modification.
*   Ensure that a PRQL query targeting sql.mssql, which deduplicates and limits results, compiles to SQL like: 
    *   `SELECT DISTINCT "District" FROM t ORDER BY "District" OFFSET 0 ROWS FETCH FIRST 100 ROWS ONLY`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.