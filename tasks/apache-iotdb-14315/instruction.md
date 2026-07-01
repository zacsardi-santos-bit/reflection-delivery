Implement the missing features for the table-model deletion functionality to ensure reliable storage, compaction, and recovery of deletion entries. Update the relevant classes to handle database names, batch writes, compaction, and legacy file upgrades.

*   Update `RelationalDeleteDataNode`:
    *   Modify the constructor to accept a `String databaseName` as a nullable third parameter.
    *   Ensure serialization and deserialization processes include the `databaseName` field, handling null values appropriately.

*   Enhance `ModificationFile`:
    *   Implement a batch write method `write(List<ModEntry> entries)` to atomically write multiple deletion entries.
    *   Ensure `compact()` merges `TableDeletionEntry` records with the same table name and predicate into a single entry when the file size is ≥ 1 MB. Avoid aggressive compaction for files < 1 MB.
    *   Handle independent compaction for distinct table-predicate groups.

*   Update `DeletionPredicate`:
    *   Add a constructor `DeletionPredicate(String tableName)` that accepts only a table name.

*   Modify `TsFileResource`:
    *   Implement `upgradeModFile(ExecutorService executor)` to upgrade legacy modification files:
        *   Run synchronously if `executor` is null, or asynchronously if `executor` is provided.
        *   Ensure the old v1 file is deleted, the new file exists, and entries are preserved as `TreeDeletionEntry` objects.
    *   Ensure `getAllModEntries()` returns all modification entries.
    *   Implement `exclusiveModFileExists()` to confirm the presence of the new-format file.
    *   Ensure `getModFileForWrite()` returns a writable `ModificationFile` that supports concurrent or post-upgrade writes.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.