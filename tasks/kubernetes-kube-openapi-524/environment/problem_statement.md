# Migrate random data generation library from archived package to actively-maintained successor

## Description

The project currently depends on a random data generation library that has been archived and is no longer actively maintained. The Kubernetes ecosystem has standardized on a newer, officially-supported successor library that offers the same capabilities with a slightly updated method naming convention.

The test files in the repository have been updated to call the new library's API, but the module dependency files haven't been updated, so the code no longer compiles. Additionally, any non-test source files that expose fuzz helper functions (used by the tests) also need to be updated to use the new library types.

## Expected Behavior

- The module dependency on the archived library should be removed
- The new replacement library should be added to the module dependencies
- All source and test files compile successfully without referencing the archived library
- The fuzz tests for schema mutation, reference replacement, and serialization roundtripping continue to work correctly

## Why This Matters

Keeping an archived, unmaintained dependency creates maintenance burden and may block adoption of other updated tooling. The new library is the designated successor and is already used elsewhere in the Kubernetes project ecosystem. Completing this migration keeps the project's dependency graph current and reduces technical debt.
