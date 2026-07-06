Update the Rust workspace configuration to recognize a new test crate for MySQL async integration tests. Ensure that this new crate can be built and run independently from the existing connector package.

*   Modify the top-level `Cargo.toml` file:
    *   Add `src/tests/mysql_test` to the `members` list to include the `risingwave_mysql_test` package as part of the workspace.
*   Update the `Cargo.lock` file:
    *   Ensure it includes the `risingwave_mysql_test` package entry with its dependencies: `futures`, `madsim-tokio`, `mysql_async`.
*   Verify the test command:
    *   Ensure the command `cargo nextest run --package risingwave_mysql_test --package risingwave_sqlparser --profile ci --no-fail-fast` completes without package-resolution errors.
    *   Confirm that all 231 pre-existing `risingwave_sqlparser` tests pass after the workspace change.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.