Update the SQL driver capability system to include support for row-level locking. Correct the MySQL driver configuration to accurately reflect its capabilities regarding CTE-with-update support. Ensure that each SQL backend (MySQL, PostgreSQL, and SQLite) declares its correct capabilities.

*   Modify the `CapabilitySql` struct in `crates/toasty-core/src/driver/capability.rs`:
    *   Add a new field `select_for_update: bool` to indicate support for row-level locking (using SELECT FOR UPDATE).
    *   Ensure the struct includes both `cte_with_update: bool` and `select_for_update: bool`.

*   Configure the SQL capabilities for each database:
    *   For MySQL:
        *   Set `cte_with_update` to `false`.
        *   Set `select_for_update` to `true`.
    *   For PostgreSQL:
        *   Set `cte_with_update` to `true`.
        *   Set `select_for_update` to `true`.
    *   For SQLite:
        *   Set `cte_with_update` to `false`.
        *   Set `select_for_update` to `false`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.