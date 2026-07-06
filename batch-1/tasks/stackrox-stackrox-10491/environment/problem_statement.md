## Description

Starting from release 4.5, the system should no longer support upgrading from or restoring backups of database versions predating the 4.0 release. Previously, this migration path was allowed and handled internally, but continuing to maintain it is unnecessary and adds complexity. Any attempt to upgrade or restore from such legacy database data should now result in an explicit, informative error rather than silently proceeding with undefined behavior.

Additionally, the internal interfaces used to select the database clone to migrate have accumulated parameters that are no longer needed (specifically a flag for "restore from legacy database"). These should be cleaned up to simplify the API.

## Expected Behavior

- When the system detects a database from before version 4.0 and attempts to migrate it into the current Postgres-based system, it should immediately return a clear error indicating that upgrades from such legacy releases are no longer supported as of the 4.5 release.
- Similarly, when a restore operation is attempted from a backup originating from a pre-4.0 release, the system should reject it with a clear error indicating that restores from such legacy releases are no longer supported as of the 4.5 release.
- The clone manager interfaces should be simplified to remove the now-unused legacy-restore parameter from the method that determines which database clone to migrate.
- Migration-related helper operations should propagate errors to callers rather than ignoring them silently.

## Why This Matters

Keeping dead code paths around for migration scenarios that are no longer valid creates confusion and potential for bugs. Explicitly rejecting unsupported migration paths makes the system's behavior predictable and helps operators understand immediately why an operation has failed rather than observing silent misbehavior.
