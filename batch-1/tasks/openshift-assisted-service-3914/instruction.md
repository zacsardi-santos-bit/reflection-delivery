Implement a new database migration to remove the obsolete `ignition_config_overrides` column from the `clusters` table in a safe and idempotent manner. Additionally, remove an outdated migration related to infrastructure environment population and update existing tests to reflect the current data model.

*   Create a new migration:
    *   File: `internal/migrations/20220527210238_drop_cluster_ignition_overrides.go`
    *   Function: `dropClusterIgnitionOverrides() *gormigrate.Migration`
    *   Use IF EXISTS semantics to drop the `ignition_config_overrides` column from the `clusters` table.
    *   Ensure the migration succeeds whether the column is present or absent.
    *   Register this migration in the `post()` function in `internal/migrations/migrations.go`.

*   Remove the outdated migration:
    *   Delete the file `internal/migrations/20210713123129_populate_infra_env.go`.
    *   Remove the `populateInfraEnv()` call from the `pre()` function in `internal/migrations/migrations.go`.

*   Update the data model and tests:
    *   Ensure `models/cluster.go` and any vendored copies do not include the `IgnitionConfigOverrides` field in the `Cluster` struct.
    *   Update existing tests for the `changeOverridesToText` migration to use the `InstallConfigOverrides` field on the `Cluster` struct.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.