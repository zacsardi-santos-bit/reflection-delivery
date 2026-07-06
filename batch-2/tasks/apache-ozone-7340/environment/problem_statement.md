## Description

The container replication service currently supports two modes of data transfer, controlled by a boolean flag passed at construction time. This dual-mode design has caused the entire test suite for this service to be duplicated: once for the standard mode and once for the optimized transfer mode. The abstract base class pattern used to share test logic between the two variants adds unnecessary complexity without providing real value.

## Expected Behavior

- The replication service should operate as a single, unified implementation without requiring callers to select a transfer mode via a boolean parameter.
- The configuration for enabling or disabling the optimized transfer mode should be removed from the service setup.
- All tests that exercised the duplicate "zero-copy" variant (separate subclasses for both the replication service and the EC key output stream tests) should be removed.
- The EC key output stream test class should stand on its own as a concrete class rather than extending an abstract base.

## Why This Matters

Maintaining two parallel code paths and test suites for the same functionality adds maintenance burden and slows down the test cycle. Removing the feature toggle and consolidating into a single implementation reduces duplication, simplifies the API, and makes the codebase easier to maintain going forward.
