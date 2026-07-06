## Upgrade Python environment management dependency

### Description

The library we use for Python environment management has released a newer version in which one of its internal modules was renamed. Our codebase still references the old module name, which causes compilation failures in integration tests. We need to update all references throughout the source code and build configuration to use the new module name.

### Expected Behavior

- The build configuration should declare the renamed dependency (no longer referencing the old name) so the project compiles cleanly.
- All source files that previously referenced the old module name should be updated to the new name.
- Integration tests that rely on Python environment introspection should compile and pass.

### Additional Feature

The example test runner script should gain an optional cleanup flag. When this flag is passed, the script should run a cleanup operation on each example project before executing its tests. If the cleanup fails, the example should be counted as failed and the script should move on to the next one.

### Why This Matters

Without the rename, the codebase fails to build because the old module no longer exists in the newer version of the library. Keeping up with this rename lets us take advantage of the latest fixes in the upstream library. The optional cleanup flag is useful for CI and reproducible testing scenarios where a pristine environment is desired before each test run.
