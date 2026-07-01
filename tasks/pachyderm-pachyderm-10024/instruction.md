Update the `clusterstate` package to make the version 2.8.0 migration state publicly accessible. This will allow external packages to reference this state directly, facilitating testing of migration steps in isolation.

*   Export the migration state:
    *   Declare a public variable named `State_2_8_0` of type `migrations.State`.
    *   Ensure this variable represents the complete database schema state after applying all version 2.8.0 migrations.
    *   Define `State_2_8_0` in `src/internal/clusterstate/v2.8.0.go`.

*   Update internal references:
    *   Modify all internal references within the `clusterstate` package to use `State_2_8_0` instead of the previously unexported variable.
    *   Ensure these updates allow the package to compile without errors.

*   Ensure functionality:
    *   Verify that when `State_2_8_0` is used with the migration application function, it results in the database reaching the 2.8.0 schema state without errors.
    *   Confirm that when `State_2_8_0` is used with the migration blocking/awaiting function, it succeeds without errors once the database has reached the 2.8.0 schema state.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.