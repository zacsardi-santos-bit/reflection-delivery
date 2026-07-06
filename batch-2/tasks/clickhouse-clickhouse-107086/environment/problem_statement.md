## Description

A stateless SQL test for array normalization operations using an experimental floating-point type has become outdated. The test file and its expected output reference file are no longer valid and should be removed from the repository to keep the test suite clean.

## Expected Behavior

- The stateless SQL test for experimental floating-point type array normalization is removed from the stateless tests directory.
- The corresponding reference file with expected output is also removed from the stateless tests directory.

## Why This Matters

Leaving stale or superseded test files in the suite can cause false failures in CI or mislead contributors about expected behavior. Removing both the test and its reference file ensures the suite only contains tests that reflect the current state of the system.
