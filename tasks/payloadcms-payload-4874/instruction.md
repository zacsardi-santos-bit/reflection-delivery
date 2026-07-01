Implement a "force accept" mode for database migration commands to bypass interactive prompts, enabling automated testing. Ensure each test suite uses its own migration directory to prevent interference. Create a shared utility for configuring environment paths for test suite directories.

*   Update the `migrate` function in `packages/payload/src/bin/migrate.ts`:
    *   Accept a `forceAcceptWarning` boolean in `parsedArgs`.
    *   Bypass interactive prompts when `forceAcceptWarning` is true.
    *   For `migrate:create`, pass `forceAcceptWarning` to `createMigration`.

*   Modify the `migrate:fresh` command:
    *   Drop and reapply migrations when `forceAcceptWarning` is true.
    *   Record results in `payload-migrations` with `name` and `batch` fields.

*   Ensure `migrate:create`:
    *   Creates migration files in `payload.db.migrationDir`.
    *   Includes the migration name in the filename.

*   Record applied migrations in `payload-migrations`:
    *   Include `name` and `batch` fields, with `batch` set to 1 for the first batch.

*   Ensure `migrate:status` runs without errors after migrations are applied.

*   Update `migrateFresh` method in:
    *   `packages/db-mongodb/src/migrateFresh.ts` and `packages/db-postgres/src/migrateFresh.ts`:
        *   Accept `{ forceAcceptWarning?: boolean }`.
        *   Skip prompts if `forceAcceptWarning` is true.

*   Update `BaseDatabaseAdapter` interface in `packages/payload/src/database/types.ts`:
    *   Modify `migrateFresh` to accept `{ forceAcceptWarning?: boolean }`.

*   Update `CreateMigration` type in `packages/payload/src/database/types.ts`:
    *   Include `forceAcceptWarning` boolean in `args`.

*   Implement `setTestEnvPaths` function in `test/helpers/setTestEnvPaths.ts`:
    *   Accept a directory path string.
    *   Set `PAYLOAD_CONFIG_PATH` to `config.ts` and `PAYLOAD_TS_OUTPUT_PATH` to `payload-types.ts` in the directory.
    *   Return true if `config.ts` exists, false otherwise.

*   Ensure `buildConfigWithDefaults` test helper:
    *   Computes migration directory based on test suite directory.
    *   Places db adapter config before `testConfig` spread.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.