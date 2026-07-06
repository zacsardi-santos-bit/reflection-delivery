Update all crates in the Rust workspace to ensure consistent versioning and successful compilation of the no-std integration tests. Align the version numbers across the workspace to resolve dependency issues.

*   Update the version numbers of all workspace crates to 0.15.0.
    *   Include the core library crate and its ndarray backend crate in this update.
    *   Ensure that the version numbers in the `burn-no-std-tests` package match the updated versions: `burn = '0.15.0'`, `burn-ndarray = '0.15.0'`.
*   Update all transitive workspace dependencies to version 0.15.0.
    *   Ensure that all dependencies within the workspace reflect this version change to maintain consistency.
*   Verify that the no-std integration test, specifically `test_mnist_model_with_random_input` in the `burn-no-std-tests` package, compiles and runs successfully after the version updates.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.