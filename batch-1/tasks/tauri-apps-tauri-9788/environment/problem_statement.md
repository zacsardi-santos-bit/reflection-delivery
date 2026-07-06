## Description

The Tauri framework's test infrastructure is broken because the mock window dispatcher used in tests is missing a required method for setting the title bar style dynamically on macOS. When the window dispatch trait was extended with a new method to support runtime title bar style changes, the corresponding mock implementation was not updated to include that method.

This causes the entire crate test suite to fail to compile, preventing all tests from running — even ones unrelated to title bar functionality.

## Expected Behavior

- The mock window dispatcher used in tests should implement all methods required by the window dispatch trait
- The test suite should compile and all existing tests should pass
- The mock implementation of the title bar style method can be a no-op stub (returning success without performing any real action), consistent with how other window operations are handled in the mock

## Why This Matters

Because the mock is missing a trait method, the entire test suite for the crate fails to compile. No tests can run at all until the mock is updated. This blocks development and CI for anyone working on the crate.
