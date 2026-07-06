Update all package version numbers in the Wasmer workspace to the next minor version release. Ensure consistency across all manifest files and regenerate the lock file to reflect these changes, allowing the project to build successfully under strict lock file enforcement.

*   Update the workspace-level package version:
    *   Change the version from 4.3.2 to 4.3.3 in the root `Cargo.toml`.
    *   Update all internal dependency references that pin to `=4.3.2` to `=4.3.3`.

*   Update library and tool dependencies:
    *   Change all references to internal workspace packages from version `=4.3.2` to `=4.3.3` in all `Cargo.toml` files.

*   Update specific package dependencies:
    *   Change dependencies on wasmer-wasix and related WASI-extension packages from version `=0.22.0` to `=0.23.0`.
    *   Update the virtual-fs dependency from version `0.13.0` to `0.14.0`.

*   Update independent package versions:
    *   Change the wasmer-journal package version from `0.4.0` to `0.5.0` in its `Cargo.toml` and all references.
    *   Update the wasmer-registry package version from `5.14.0` to `5.15.0` in its `Cargo.toml` and all references.
    *   Modify the wasmer-api (backend-api) package version from `0.0.30` to `0.0.31` in its `Cargo.toml` and all references.

*   Regenerate the lock file:
    *   Update the `Cargo.lock` file to reflect all new version numbers, ensuring compatibility with strict lock file enforcement (`--locked`).

*   Validate the changes:
    *   Ensure that running the wasmer-types test suite with strict lock file enforcement results in all 37 unit tests passing. These tests cover areas such as entity collections, feature flag configuration, error handling, and more.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.