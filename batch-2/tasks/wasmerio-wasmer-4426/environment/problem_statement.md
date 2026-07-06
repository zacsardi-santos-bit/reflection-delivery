## Description

The WebAssembly validation library is pinned to a very old version of a wasm parsing dependency. This older version has a completely different API from the current releases — the way validators are created, feature flags are configured, and validation is run has changed substantially in newer versions. As a result, the codebase cannot be updated to the newer dependency version without also updating all the code that uses the old API.

## Expected Behavior

- The WebAssembly interface library should be able to use the current workspace-wide version of the wasm parsing dependency rather than its own old pinned version
- The validation code should use the modern validator API that accepts a features configuration struct (enabling threads, reference types, SIMD, bulk memory, and multi-value features) and provides a single method to validate an entire wasm binary at once
- After migrating to the new API, validation of global imports and exports against an interface should continue to work correctly — returning appropriate errors when types don't match
- A minimal WebAssembly module should validate successfully using the new API

## Why This Matters

Currently, keeping an old pinned dependency version prevents the project from consolidating to a single version of the wasm parser across all crates. The old API is no longer available in newer releases, so any attempt to upgrade the dependency causes compilation failures across the library. Updating the code to use the modern API will allow the workspace to use a consistent, up-to-date version of the parser everywhere, and all existing validation tests will continue to pass.
