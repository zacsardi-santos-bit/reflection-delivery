## Description

The no-std compatibility test suite in this workspace is currently failing to compile because it references a newer release version of the core libraries than what is actually declared across the workspace. The test package was updated to depend on the upcoming release, but the actual library crates and all their siblings in the workspace still carry the old release version number. This version mismatch causes the build system to fail during dependency resolution before any tests can run.

## Expected Behavior

- All crates in the workspace should have their version numbers updated consistently to reflect the new release
- The no-std integration tests should compile and pass once all crate versions are aligned
- There should be no dependency resolution errors related to version mismatches between workspace crates

## Why This Matters

Having inconsistent version numbers within a workspace prevents compilation entirely, blocking all testing and release workflows. A uniform version bump across all workspace crates is required so that the library can be properly released at its new version and the integration tests can validate its correctness.
