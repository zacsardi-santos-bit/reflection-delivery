# Refactor CLI entry point and add automated end-to-end tests

## Description

The Tact compiler's CLI behavior is currently validated through a series of shell script steps in CI rather than a proper automated test suite. This means there's no easy way to run these CLI tests locally, no structured pass/fail reporting, and the tests are fragile to environment differences. Additionally, the programmatic interface for running compilations currently takes file paths and configuration file locations, making it difficult to test the pipeline in isolation without touching the filesystem.

## Expected Behavior

- CLI behavior (version output, single-file compilation, config-based compilation, flag handling, expression evaluation, error handling) should be covered by an automated test suite that can be run with the existing test framework
- The programmatic API for running the Tact compiler should accept in-memory configuration objects and virtual file system instances instead of file paths, enabling isolated and deterministic testing
- Compilation errors should not expose internal stack traces to users
- The CLI should return non-zero exit codes on failure and clean zero exit codes on success
- Mutually exclusive mode flags should be properly rejected with an appropriate exit code
- Unknown or unrecognized flags should be rejected with a non-zero exit code

## Why This Matters

Moving from CI shell scripts to a proper automated test suite gives developers faster feedback, the ability to run tests locally, and better error reporting. Improving the programmatic API to accept in-memory configuration and virtual file systems makes it easier to build tooling and tests on top of the compiler without requiring filesystem access.
