Implement a dedicated SQL generation module to safely fetch rows from database objects such as tables, views, materialized views, and foreign tables. Ensure the module handles large text values, array columns, and special column names correctly, while supporting truncation, filtering, sorting, and pagination.

*   Create a new file at `packages/pg-meta/src/query/table-row-query.ts` and export the following functions:
    *   `getDefaultOrderByColumns(table: { primary_keys: Array<{ name: string }>, columns: Array<{ name: string }> }): string[]`
        *   Return an empty array if both `primary_keys` and `columns` are empty.
        *   Return primary key names if they exist; otherwise, return the first column name.
    *   `getTableRowsSql(args: { table: PGTable | PGView | PGForeignTable | PGMaterializedView, filters?: Filter[], sorts?: Sort[], page?: number, limit?: number, maxCharacters?: number, maxArraySize?: number }): string`
        *   Generate a SQL SELECT statement for any table-like entity.
        *   Use schema-qualified table names in the FROM clause: `from {schema}.{table_name}`.
        *   End the generated SQL string with a semicolon.
        *   Apply truncation for text and JSON columns using `octet_length()` and `left()`, defaulting `maxCharacters` to 10240.
        *   For array columns, truncate based on `maxCharacters` and `maxArraySize`, defaulting `maxArraySize` to 50.
        *   Select non-text, non-array columns without transformation.
        *   Quote column names with special characters, escaping embedded quotes.
        *   Include a default ORDER BY using primary key columns if no sorts are provided.
        *   Support optional filters, sorts, and pagination using LIMIT and OFFSET.
        *   Allow `maxCharacters` and `maxArraySize` to override defaults.

*   Ensure the SQL format for text/JSON columns is:
    ```
    case
        when octet_length({col}::text) > {maxCharacters} 
        then left({col}::text, {maxCharacters}) || '...'
        else {col}::text
    end as {col}
    ```
*   Ensure the SQL format for array columns is:
    ```
    case 
        when octet_length({col}::text) > {maxCharacters} 
        then (select array_cat({col}[1:{maxArraySize}]::text[], array['...']))::text[]
        else {col}::text[]
    end
    ```
*   Maintain the column selection order based on `ordinal_position`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.