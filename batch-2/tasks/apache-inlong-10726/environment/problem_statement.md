## Description

The project currently provides end-to-end test infrastructure for older versions of its stream processing runtime, but it does not have a corresponding test module for the latest major version it now supports. This means integration tests for data connectors cannot be run against the newer runtime, leaving a gap in test coverage.

## Expected Behavior

- A new end-to-end test module targeting the newer runtime version should exist and be buildable using the project's standard build tooling.
- The module should activate automatically as the default profile so that CI can run it without extra configuration.
- The module should include shared test utility classes, in particular a utility that resolves named placeholders in template strings (e.g., in SQL scripts or configuration files) by substituting values from a key-value map at runtime.
- The placeholder resolver should handle multiple named tokens in a single string, replacing each one with the corresponding value supplied at call time.
- The build dependency structure for format libraries should be reorganized so that all required format modules are available under the new runtime profile.

## Why This Matters

Without this module, the project has no automated way to catch integration-level regressions that are specific to the newer runtime version. Adding this infrastructure allows developers to write and run end-to-end tests for data pipeline connectors against the new version, and the parameterized template utility makes it practical to share and reuse test SQL scripts across different test scenarios.
