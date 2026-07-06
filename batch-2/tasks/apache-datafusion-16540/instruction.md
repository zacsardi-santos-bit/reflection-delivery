Implement a new SQL dialect for DataFusion's SQL unparser that targets BigQuery. Ensure that column aliases with special characters are encoded to be compatible with BigQuery's identifier rules, and apply this encoding consistently across the SQL query.

*   Create a new struct `BigQueryDialect` in `datafusion/sql/src/unparser/dialect.rs`.
    *   Ensure `BigQueryDialect` is publicly available and can be imported from `datafusion_sql::unparser::dialect`.
    *   Implement the `Dialect` trait for `BigQueryDialect`.
    *   Provide a constructor `BigQueryDialect::new()` for creating instances.

*   Implement identifier quoting and encoding:
    *   Use backticks (`) as the identifier quote style for all identifiers, including column names and aliases.
    *   Encode special characters in column aliases by replacing each with an underscore followed by its decimal Unicode code point.
        *   Examples: '(' becomes '_40', ')' becomes '_41', '*' becomes '_42', '@' becomes '_64'.
    *   Apply this encoding to both alias definitions in SELECT clauses and any references to those aliases (e.g., in WHERE clauses).

*   Ensure compatibility with non-BigQuery dialects:
    *   When using the default (non-BigQuery) dialect, preserve column aliases with special characters as-is.
    *   Quote these aliases using double quotes, without applying any encoding.

*   Handle mixed queries:
    *   Ensure that queries with a mix of special-character and non-special-character aliases are processed correctly.
    *   Only apply encoding to aliases that require it, leaving others unchanged.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.