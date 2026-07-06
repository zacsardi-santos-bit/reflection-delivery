Implement the cleanup of physical data files when a table is dropped in GlareDB. Ensure that the table's storage directory is empty after the drop operation completes. Make the `execute` method on `LocalSession` publicly accessible to facilitate integration testing.

*   Update the `execute` method in `crates/glaredb/src/local.rs`:
    *   Change the method signature to `pub async fn execute(&mut self, text: &str) -> Result<()>` to make it publicly accessible.
    *   Ensure the method can be called from integration tests and external code.

*   Implement physical file cleanup for dropped tables:
    *   When a `DROP TABLE` statement is executed, remove the physical data files associated with the table.
    *   Ensure the storage directory for the dropped table contains no regular files after the drop operation completes.
    *   Perform the file deletion only after the table has been removed from the catalog to maintain consistency.

*   Ensure the storage directory path follows the format:
    *   `'databases/<database-uuid>/tables/<table-oid>'` relative to the configured storage root location.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.