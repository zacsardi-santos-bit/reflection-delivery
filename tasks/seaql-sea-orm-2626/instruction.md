Implement a structured error variant to handle unsupported database backends in a Rust ORM library. Update the `DbErr` enum to include a new variant for unsupported operations and add a method to retrieve the backend name as a string.

*   Add a new struct variant `BackendNotSupported` to the `DbErr` enum in `src/error.rs`.
    *   Include two fields: `db: &'static str` for the backend name and `ctx: &'static str` for the operation or context name.
*   Update the `DatabaseBackend` type in `src/database/db_connection.rs` with a new method:
    *   Implement `fn as_str(&self) -> &'static str` to return the backend's name.
        *   Return "MySql" for MySQL, "Postgres" for PostgreSQL, and "Sqlite" for SQLite.
*   Ensure the `BackendNotSupported` variant can be used in match catch-all arms.
    *   Construct the error as `DbErr::BackendNotSupported { db: backend.as_str(), ctx: "operation_name" }`.
    *   Use the field names `db` and `ctx` exactly as specified.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.