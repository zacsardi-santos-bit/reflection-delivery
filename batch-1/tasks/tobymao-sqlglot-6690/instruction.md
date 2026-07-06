Implement the necessary changes to ensure correct transpilation of Snowflake time conversion functions to other SQL dialects, specifically addressing compact time format strings and the use of safe/nullable operations.

*   Update the transpilation logic for TO_TIME when receiving a TIMESTAMP-typed argument:
    *   Snowflake: Use `TO_TIME(CAST(value AS TIMESTAMP))`.
    *   DuckDB: Use `CAST(CAST(value AS TIMESTAMP) AS TIME)`.
    *   BigQuery: Use `TIME(CAST(value AS DATETIME))`.

*   Modify the handling of TO_TIME with the format string 'HH24MISS' or 'hh24miss':
    *   Normalize the format string to lowercase in Snowflake: `TO_TIME(value, 'hh24miss')`.
    *   Transpile to DuckDB using the equivalent format: `CAST(STRPTIME(value, '%H%M%S') AS TIME)`.

*   Adjust the transpilation of TRY_TO_TIME with the format string 'HH24MISS' or 'hh24miss':
    *   Normalize the format string to lowercase in Snowflake: `TRY_TO_TIME(value, 'hh24miss')`.
    *   Use safe operations in DuckDB: `TRY_CAST(TRY_STRPTIME(value, '%H%M%S') AS TIME)`.

*   Ensure TRY_TO_TIME without a format string is correctly transpiled:
    *   Snowflake: Use `TRY_CAST(value AS TIME)`.
    *   DuckDB: Use `TRY_CAST(value AS TIME)`.

*   For any TRY_TO_TIME call with a format string in DuckDB:
    *   Use `TRY_STRPTIME` for parsing and `TRY_CAST` for casting: `TRY_CAST(TRY_STRPTIME(value, format) AS TIME)`.

*   Annotate both TO_TIME and TRY_TO_TIME with the format string 'HH24MISS' as returning the TIME data type in the Snowflake dialect context.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.