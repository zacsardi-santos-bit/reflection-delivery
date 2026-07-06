## Description

Our test suite currently depends on a deprecated, unmaintained third-party HTTP client library. This library is no longer actively developed, poses maintenance and security risks, and adds unnecessary bloat to our dependencies. We should remove it entirely and replace all usages in tests with built-in networking support.

## Expected Behavior

- The deprecated HTTP client library is completely removed from the project's declared dependencies so it is never installed
- All test files that currently use this library are updated to use the built-in HTTP module instead
- A shared helper utility is added to the sanitize-field-names test directory to handle form-encoded POST requests (since the built-in module doesn't do this automatically, and multiple test files need this capability)
- The helper is shared across all relevant web framework integration tests
- The cloud metadata and HTTP instrumentation tests are similarly migrated to use the built-in HTTP module directly

## Why This Matters

Relying on a deprecated package risks security vulnerabilities and compatibility issues over time. Migrating to built-in capabilities makes the project more self-contained, reduces supply-chain risk, and ensures long-term maintainability without depending on unmaintained open source software.
