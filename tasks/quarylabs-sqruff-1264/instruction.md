Implement support for parsing ALTER AGGREGATE statements in the PostgreSQL dialect of a SQL linter and formatter. Ensure the parser can handle renaming aggregates, changing their owner, moving them to a different schema, and using wildcard arguments. Additionally, update the parser to correctly handle all standard join syntax variants in PostgreSQL.

*   Add a new grammar rule for ALTER AGGREGATE statements in `crates/lib-dialects/src/postgres.rs`.
    *   Register this rule in the `statement_segment()` function.
*   Ensure the parse tree for ALTER AGGREGATE includes:
    *   Aggregate name as `object_reference` containing `naked_identifier`.
    *   Argument list as `bracketed` containing `word` tokens or `star` for wildcard.
    *   `RENAME TO` target as `function_name` containing `function_name_identifier`.
    *   `OWNER TO <role_name>` as `role_reference` containing `naked_identifier`.
    *   `OWNER TO CURRENT_ROLE/CURRENT_USER/SESSION_USER` as `keyword` nodes.
    *   `SET SCHEMA <name>` as `table_reference` → `object_reference` → `naked_identifier`.
*   Update the parser to handle standard JOIN types in SELECT statements:
    *   Parse INNER JOIN, LEFT JOIN, LEFT OUTER JOIN, RIGHT JOIN, RIGHT OUTER JOIN, FULL JOIN, FULL OUTER JOIN, and CROSS JOIN into `join_clause` segments.
    *   Ensure JOINs with an ON condition produce a `join_on_condition` segment.
    *   Ensure CROSS JOINs do not produce a `join_on_condition` segment.
    *   Parse comma-separated tables in the FROM clause into multiple `from_expression` segments.

**CRITICAL:** Do not modify any files in the `tests/` directory.  
The verification system will apply test patches automatically.  
Your task is to implement the feature in source files only.