Implement a database migration to detect and fix duplicate pipeline version numbers, ensuring uniqueness and updating related job records. Export necessary states and functions to facilitate testing and integration with other packages.

*   Export the migration state variable `State_2_8_0` from `src/internal/clusterstate/v2.8.0.go` to make it accessible to other packages.
*   Refactor the v2.10.0 migration chain:
    *   Implement `Migrate_v2_10_BeforeDuplicates` to apply all migration steps up to deduplication.
    *   Ensure the main `Migrate` function chains `Migrate_v2_10_BeforeDuplicates` with `DeduplicatePipelineVersions`.
*   Implement `DeduplicatePipelineVersions` in `src/internal/clusterstate/v2.10.0/pipelines.go`:
    *   Back up `collections.pipelines` and `collections.jobs` tables.
    *   Renumber duplicate pipeline versions sequentially.
    *   Update job records to reference corrected versions.
    *   Create a unique index `pip_version_idx` on `collections.pipelines(idx_version)`.
    *   Return an error if any step fails.
*   Ensure post-migration functionality:
    *   Listing pipelines and jobs must succeed and return correct entries.
    *   Running new pipeline versions must succeed without error.
*   Define `CreateUniqueIndex` as an exported string variable containing the SQL for creating the unique index.
*   Define `UpdatesBatchSize` as an exported integer variable (default 100) for controlling batch sizes in updates.
*   Define `PipUpdateRow` as an exported struct with fields `Key`, `Proto`, and `IdxVersion`.
*   Implement `VersionKey` to return a formatted string key for pipeline versions.
*   Implement `UpdatePipelineRows` to batch-update pipeline rows using `UpdatesBatchSize`.
*   Implement `UpdateJobPipelineVersions` to update job records based on a version-change map.
*   Export `FullOption` struct from `src/internal/pachd/full.go` with a `DesiredState` field.
*   Update `NewFull` function to accept a `*FullOption` parameter, using `opt.DesiredState` if provided.
*   Add `MutateFullOption` field to `TestPachdOption` in `src/internal/pachd/testpachd.go` to allow test configuration overrides.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.