Implement changes to the database migration system to discontinue support for upgrading from or restoring backups of database versions predating release 4.0. Ensure that attempts to perform such operations result in explicit error messages. Simplify the internal interfaces by removing obsolete parameters and ensure that migration-related methods propagate errors to their callers.

*   Update the `GetCloneToMigrate` method in the Postgres clone manager:
    *   Remove the `restoreFromRocks` boolean parameter.
    *   Change the method signature to `GetCloneToMigrate(rocksVersion *migrations.MigrationVersion) (string, bool, error)`.
    *   Return an error with the message "Effective release 4.5, upgrades from pre-4.0 releases are no longer supported." when `rocksVersion` is non-nil and indicates a pre-4.0 migration is needed. Also, return an empty string for the clone name and `migrateRocks=true`.
    *   When `rocksVersion` is nil, return the appropriate clone name and `migrateRocks=false` without error.

*   Modify the `restoreCentral` mock helper in `migrator/clone/mock_central.go`:
    *   Return an error with the message "Effective release 4.5, restores from pre-4.0 releases are no longer supported." when `rocksToPostgres` is true.

*   Update mock central helper methods to return errors:
    *   `runMigrator(breakPoint string, forceRollback string) error`
    *   `rebootCentral() error`
    *   `migrateWithVersion(ver *versionPair, breakpoint string, forceRollback string) error`
    *   `upgradeCentral(ver *versionPair, breakpoint string) error`
    *   `rollbackCentral(ver *versionPair, breakpoint string, forceRollback string) error`
    *   `restoreCentral(ver *versionPair, breakPoint string, rocksToPostgres bool) error`

*   Update the top-level `DBCloneManager` interface:
    *   Change `GetCloneToMigrate` to return `(string, error)` instead of `(string, string, string, error)`.
    *   Update `Persist` to accept only `(pgClone string)` instead of `(clone string, pgClone string, persistBoth bool)`.

*   Remove support for the Rocks-to-Postgres restore migration path:
    *   Eliminate any code paths that handled restoring from RocksDB to Postgres using `restoreFromRocks=true`.

*   Remove the `TestRollbackPostgresToRocks` test and related `createAndRunCentralStartRocks` helper, as rolling back from Postgres to RocksDB is no longer supported.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.