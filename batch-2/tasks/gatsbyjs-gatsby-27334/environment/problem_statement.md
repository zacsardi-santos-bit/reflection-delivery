## Description

Gatsby plugin authors currently have no standardized way to write unit tests for their plugin's option schema validation logic. When a plugin defines what options it accepts and what types those options must be, there is no built-in utility to verify that the schema correctly rejects invalid values or accepts valid ones — without needing to trigger a full build process.

## Expected Behavior

- Plugin developers should be able to pass a plugin schema definition along with a partial set of test option values to a dedicated testing utility.
- The utility should return a structured result indicating whether the provided options are valid, along with a list of human-readable error messages for any failing fields.
- The utility should support testing a subset of the schema's fields at a time — only the fields included in the test input should be checked.
- Error messages should clearly identify the field name and the nature of the validation failure (wrong type, missing required value, invalid array item, etc.).
- The utility should be exported from the plugin utilities package so it is easy for plugin authors to import in their own test suites.

## Why This Matters

Without this utility, plugin authors either skip testing their option schemas entirely or write significant boilerplate to replicate validation behavior. A first-class testing helper lowers the barrier to writing schema tests and helps ensure Gatsby plugins have reliable, well-validated option handling.
