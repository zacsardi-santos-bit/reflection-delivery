## Description

The integration tests for async process standard I/O operations fail to compile when using the expected feature flag combination. The test package's manifest is missing a dedicated feature group that bundles the async runtime and I/O capabilities needed for these tests to build and run. As a result, the entire process I/O test suite cannot be executed.

## Expected Behavior

- A new feature flag should be defined in the integration test package's manifest that groups the necessary async runtime and I/O capabilities together
- When running the process I/O integration tests with this feature flag enabled, the following test scenarios should all compile and pass:
  - Non-blocking check of whether a spawned child process has exited
  - Verifying that pipes are properly closed when a child process exits
  - Capturing the standard output of a process after it completes
  - Piping the output of one spawned process into another
  - Feeding a large volume of data through a process's standard input and reading it back from standard output

## Why This Matters

The CI test command for this suite explicitly requests a feature flag that does not yet exist, causing an immediate compilation failure before any tests can run. Adding the missing feature definition unblocks the entire process I/O integration test suite. Additionally, the helper binary used in these tests should be modernized to use async I/O consistent with the rest of the test infrastructure.
