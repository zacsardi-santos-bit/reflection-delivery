Update the ntex project to use the new major version of its random number generation library. Modify the dependency version and replace old API call sites with their new equivalents to ensure the codebase builds cleanly and all existing tests pass.

*   Update the rand crate version:
    *   Change the rand crate version from 0.8.x to 0.9.x in the workspace-level Cargo.toml, using `rand = "0.9"`.
    *   Ensure the ntex crate's dev-dependency on rand resolves to version 0.9.x, either directly or through the workspace dependency.
    *   Ensure the ntex-io crate's dev-dependency on rand resolves to version 0.9.x.

*   Replace API calls in source files:
    *   Replace all instances of `rand::thread_rng()` in non-test source files within the ntex and ntex-io crates with `rand::rng()`.
    *   Replace all instances of `rand::distributions::Alphanumeric` in non-test source files within the ntex and ntex-io crates with `rand::distr::Alphanumeric`.

*   Ensure successful compilation and testing:
    *   Verify that the ntex crate compiles successfully with the tokio, compress, openssl, rustls, and cookie features enabled.
    *   Confirm that all existing tests pass after making the necessary updates.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.