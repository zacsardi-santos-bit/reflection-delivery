## Description

The container device injection code relies on an external library for Container Device Interface (CDI) support. That library has released a new major version that changes its public API — the new version replaces the registry-object pattern with simpler top-level package-level functions. The current code is pinned to an older version and uses the old API pattern.

## Expected Behavior

- The CDI library dependency should be upgraded to a newer version that exposes independent package-level functions for configuration and refresh.
- The device injection code should be updated to call these new top-level functions directly, rather than obtaining a registry object and calling methods on it.
- Configuration and refresh should be handled as separate operations, each with independent error handling, instead of a single chained call.

## Why This Matters

Without the upgrade, the codebase cannot use newer CDI features and the test code that sets up CDI using the new API will fail to compile. Keeping up with the upstream library's API evolution ensures compatibility and takes advantage of a cleaner, more explicit error-handling model for device injection failures.
