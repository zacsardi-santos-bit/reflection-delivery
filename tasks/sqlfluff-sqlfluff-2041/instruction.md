Implement support for parsing materialized view statements in SQLFluff's Postgres dialect. Ensure that the dialect can handle creating, altering, dropping, and refreshing materialized views, including various options and clauses. Correct a typo in a test fixture filename as part of this task.

*   Implement parsing for `CREATE MATERIALIZED VIEW` statements:
    *   Support optional `IF NOT EXISTS`, `USING <access_method>`, `TABLESPACE <ts>`, and `WITH (<storage_params>)`.
    *   Handle query bodies as bare or parenthesized `SELECT`.
    *   Include optional `WITH DATA` or `WITH NO DATA` clause, grouped as `with_data_clause`.
    *   Ensure the parse tree has a top-level node of type `create_materialized_view_statement`.

*   Implement parsing for `ALTER MATERIALIZED VIEW` statements:
    *   Support actions like `ALTER [COLUMN] col SET STATISTICS n`, `ALTER [COLUMN] col SET (attr_options)`, `ALTER [COLUMN] col RESET (attr_options)`, `ALTER [COLUMN] col SET STORAGE`, `CLUSTER ON`, `SET WITHOUT CLUSTER`, `SET (storage_parameters)`, `RESET (storage_parameters)`, `OWNER TO`, `DEPENDS ON EXTENSION`, `NO DEPENDS ON EXTENSION`, `RENAME`, `SET SCHEMA`, and `ALL IN TABLESPACE`.
    *   Support optional `IF EXISTS` for single-view form.
    *   Wrap column-level actions in `alter_materialized_view_action_segment`.
    *   Ensure the parse tree has a top-level node of type `alter_materialized_view_statement`.

*   Implement parsing for `DROP MATERIALIZED VIEW` statements:
    *   Support optional `IF EXISTS`, multiple views, and `CASCADE` or `RESTRICT`.
    *   Ensure the parse tree has a top-level node of type `drop_materialized_view_statement`.

*   Implement parsing for `REFRESH MATERIALIZED VIEW` statements:
    *   Support optional `CONCURRENTLY` and `WITH DATA` or `WITH NO DATA` clause.
    *   Ensure the parse tree has a top-level node of type `refresh_materialized_view_statement`.

*   Update parsing for `CREATE TABLE AS` statements:
    *   Parse `WITH [NO] DATA` clause as `with_data_clause`.
    *   Ensure correct parsing of unparenthesized `SELECT` body followed by `WITH NO DATA` or `WITH DATA`.

*   Correct the typo in the fixture filename:
    *   Rename `postgresl_create_table_as.sql` to `postgres_create_table_as.sql`.
    *   Update the corresponding YAML file to match the new filename and content.

*   Register all new statement segment types with the Postgres dialect's `StatementSegment`.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.