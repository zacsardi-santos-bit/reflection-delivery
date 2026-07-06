## Description

Across the codebase, a number of logging calls are incorrectly using an error-wrapping format verb that is only meant for creating wrapped errors. When this verb is passed to general-purpose logging functions, the output is garbled and unreadable — instead of printing the actual error message, the log line shows an internal representation of the error object. This makes log output much harder to debug when errors occur.

## Expected Behavior

- Logging calls that include error values should produce human-readable output showing the error message as a plain string.
- The error-wrapping format verb should only appear in contexts specifically designed for error wrapping — not in logging function calls.
- A codebase-wide automated check should enforce this rule going forward, failing the build whenever any logging call is found using the wrong format verb for errors.

## Why This Matters

When operators or developers review logs to diagnose problems, they rely on log messages being readable. Garbled error output like the one caused by this misuse makes root-cause analysis much harder. Fixing this consistently across all source directories (including production code, test infrastructure, and utilities) ensures that error context is always visible in logs and that the same mistake cannot be silently reintroduced in the future.
