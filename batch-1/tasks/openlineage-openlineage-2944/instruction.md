Implement a SQL lineage parser that correctly qualifies table names in multi-statement SQL scripts by tracking database and schema context-switching commands. Ensure the parser respects dialect-specific conventions and maintains context across statements.

*   Implement the function `extract_up_to_two_ident_values` in `integration/sql/impl/src/visitor.rs` with the signature:
    *   `extract_up_to_two_ident_values(object_name: &ObjectName) -> (Option<String>, Option<String>)`
    *   Return `(None, None)` for an empty identifier list.
    *   Return `(Some(value), None)` for a single identifier.
    *   Return `(Some(first), Some(second))` for two or more identifiers.
    *   Strip quote characters from quoted identifiers, returning only the raw string value.

*   Ensure the parser processes USE statements correctly:
    *   In the 'generic', 'snowflake', 'mssql', 'mysql', or 'hive' dialects, a single identifier in a USE statement sets the default database.
    *   In the 'generic' or 'snowflake' dialects, a two-part identifier (first.second) sets the default database and schema.
    *   In the 'databricks' dialect:
        *   A single identifier sets the default schema.
        *   `USE DATABASE x` sets the default schema.
        *   `USE SCHEMA x` sets the default schema.
        *   `USE CATALOG x` sets the default database.
    *   In the 'snowflake' dialect:
        *   `USE DATABASE x` sets the default database only.
        *   `USE SCHEMA x` with a single identifier sets the default schema only.
        *   `USE SCHEMA db.schema` with two identifiers sets both the default database and schema.
    *   In the 'hive' dialect, ignore `USE DEFAULT` without errors.

*   Maintain context across statements:
    *   Apply the current USE context to unqualified table references.
    *   Preserve the table's own schema if it is explicitly specified.
    *   Do not override fully-qualified table references.
    *   Preserve previously established schema when a new USE sets only the database.

*   Register the 'databricks' dialect in `integration/sql/impl/src/dialect.rs` to ensure correct dialect resolution.

*   Ensure the parsing context is shared across all SQL statements in a multi-statement parsing call.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.