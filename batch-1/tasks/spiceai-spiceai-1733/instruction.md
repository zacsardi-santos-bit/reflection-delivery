Implement a feature to convert Arrow schemas into a sequence of PostgreSQL DDL statements, accommodating both simple and structured fields. Ensure that the PostgreSQL module's test infrastructure compiles correctly by updating dependencies.

*   Update `CreateTableBuilder::build_postgres()` in `crates/arrow_sql_gen/src/statement.rs`:
    *   Change the return type from `String` to `Vec<String>`.
    *   For schemas with only simple fields, return a `Vec` with one element: the `CREATE TABLE IF NOT EXISTS` statement.
    *   For schemas with structured fields, prepend a composite type creation statement for each structured field before the `CREATE TABLE` statement.
    *   Ensure composite type creation statements are conditional, using PostgreSQL's procedural language block.

*   Fix the PostgreSQL module's test infrastructure:
    *   Update the workspace-level `tokio` dependency to include the 'macros' feature to ensure `#[tokio::test]` attributes compile.

*   Implement `TypeBuilder` in `crates/arrow_sql_gen/src/postgres/builder.rs`:
    *   Define `TypeBuilder::new(name: String, fields: &Fields) -> Self` to store the type name and fields.
    *   Implement `TypeBuilder::build(self) -> String` to generate a SQL string for conditional composite type creation.
    *   Ensure the SQL format for a type named 'person' with fields 'id' (Int32) and 'name' (Utf8) matches the specified format.

*   Re-export `TypeBuilder` as a public submodule of the `postgres` module, exposing it under `postgres::builder`.

*   Update existing tests in `statement.rs`:
    *   Modify `test_basic_table_creation` and `test_table_creation_with_primary_keys` to handle the `Vec<String>` return type by indexing into the result (e.g., `sql[0]`).

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.