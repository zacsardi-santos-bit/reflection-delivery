Implement a fluent, chainable assertion wrapper for the rustup test suite to streamline interactive installer tests. Ensure the new `Assert` struct supports pattern-based output matching and automatic host-triple substitution, and is accessible from the test utilities module.

*   Implement the `Assert` struct in `src/test/clitools.rs`:
    *   Construct `Assert` from a `SanitizedOutput` using `Assert::new(output: SanitizedOutput) -> Assert`.
    *   Ensure `Assert` is publicly exported from `rustup::test` via re-exporting in `src/test.rs`.
    *   Provide the following chainable methods:
        *   `is_ok(&self) -> &Assert`: Assert the command exited successfully.
        *   `is_err(&self) -> &Assert`: Assert the command exited with a failure status.
        *   `with_stdout(&self, expected: snapbox::str) -> &Assert`: Assert stdout matches a pattern with support for `...` wildcard and `[HOST_TRIPLE]` substitution.
        *   `with_stderr(&self, expected: snapbox::str) -> &Assert`: Assert stderr matches a pattern with support for `...` wildcard and `[HOST_TRIPLE]` substitution.
        *   `without_stdout(&self, unexpected: &str) -> &Assert`: Assert stdout does NOT contain the specified string.

*   Update the `Config` struct in `src/test/clitools.rs`:
    *   Add an `async expect` method:
        *   Signature: `expect(&self, args: impl IntoIterator<Item = impl AsRef<str>>) -> impl Future<Output = Assert>`
        *   Run a command with given arguments and return an `Assert` wrapping the output.
    *   Add an `async expect_with_env` method:
        *   Signature: `expect_with_env(&self, args: impl IntoIterator<Item = impl AsRef<str>>, env: impl IntoIterator<Item = (impl AsRef<str>, impl AsRef<str>)>) -> impl Future<Output = Assert>`
        *   Run a command with arguments and environment variables, returning an `Assert`.

*   Modify helper functions in the interactive installer test module:
    *   Ensure `run_input` and `run_input_with_env` return `Assert` by wrapping `SanitizedOutput` in `Assert::new()`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.