Implement a fix for the schema migration tool to ensure that default values and existing foreign key constraints are preserved when adding new foreign key constraints to columns. Additionally, provide a utility function to verify the existence of constraints in tests.

*   Preserve default values:
    *   Ensure that when a foreign key constraint is added to a column with a default value, the default value remains effective during and after the migration.
    *   Verify that inserts omitting the column still apply the default value correctly in the new schema view.

*   Preserve existing foreign key constraints:
    *   When adding a new foreign key constraint to a column, ensure any pre-existing foreign key constraints remain intact after the migration.
    *   Maintain the integrity of the schema by ensuring all constraints are present post-migration.

*   Manage constraint names during migration:
    *   During the migration, create the new foreign key constraint on a temporary duplicate column with a temporary name using the TemporaryName() function.
    *   After migration, rename the constraint back to its original name on the table.

*   Implement a test utility:
    *   Add a function `ConstraintMustExist` in `pkg/migrations/op_common_test.go` with the signature:
        *   `ConstraintMustExist(t *testing.T, db *sql.DB, schema, table, constraint string)`
    *   This function should query the database and fail the test if the specified constraint does not exist on the given table in the specified schema.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.