## Description

The Go bindings test suite has started failing to compile because new integration tests depend on a concurrency utility library that is not registered as a module dependency. As a result, none of the tests can run — including pre-existing tests that were passing before and have nothing to do with the new functionality.

## Expected Behavior

- The module dependency should be declared so that the package compiles successfully.
- All pre-existing local tests should continue to pass once compilation is restored.
- New tests for remote database features (connection health checks, data type scanning, and concurrent query execution on a shared connection) should compile and be gracefully skipped when no remote database is available.

## Why This Matters

Currently, the entire test suite is broken due to a missing module registration. Developers cannot run any tests — even those unrelated to the new feature — until the dependency is properly declared. Adding the missing dependency restores the ability to build and test the Go bindings module.
