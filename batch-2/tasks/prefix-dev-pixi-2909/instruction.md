Update the project's conda typing library dependencies to support the new "extras" feature. Modify the package specification types to use heap-allocated wrappers for large structs and ensure all match specifications include the new optional-feature-groups field. Ensure the project compiles successfully against the updated library versions.

*   Update dependencies in `Cargo.toml`:
    *   Set `rattler_conda_types` to version `0.30.0`.
    *   Update the full rattler family: `rattler` to `0.29.0`, `rattler_cache` to `0.3.5`, `rattler_lock` to `0.22.40`, `rattler_networking` to `0.22.0`, `rattler_repodata_gateway` to `0.21.33`, `rattler_shell` to `0.22.16`, `rattler_solve` to `1.3.5`, `rattler_virtual_packages` to `2.0.0`.

*   Modify `PixiSpec` and `BinarySpec` enums in `crates/pixi_spec/src/lib.rs`:
    *   Change `DetailedVersion` variant to hold `Box<DetailedSpec>`.
    *   Use `Box::new(DetailedSpec { ... })` for construction.
    *   Update `PixiSpec::into_version()` to dereference the box: `Self::DetailedVersion(v) => v.version`.
    *   Update `PixiSpec::into_detailed()` to return dereferenced box: `Self::DetailedVersion(v) => Some(*v)`.

*   Implement trait conversions:
    *   `From<DetailedSpec>` for `BinarySpec` and `PixiSpec` should wrap the value in a box: `Self::DetailedVersion(Box::new(value))`.

*   Update `DetailedSpec` conversion in `crates/pixi_spec/src/detailed.rs`:
    *   Ensure `NamelessMatchSpec` includes `extras` field initialized with `Default::default()`.

*   Adjust integration test helpers:
    *   In `tests/integration_rust/common/package_database.rs`, initialize `extra_depends` with `Default::default()`.

*   Ensure all workspace crates, including transitive dependencies like `pixi_utils` and `pixi_config`, compile against the updated rattler versions.
    *   Address any API changes in authentication middleware and other utilities due to `rattler_networking` update.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.