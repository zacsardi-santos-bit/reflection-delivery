## Description

When writing code that matches on the active database backend, the existing set of supported backends (MySQL, PostgreSQL, SQLite) does not cover all possible configurations. If the code encounters an unexpected or unsupported backend, there is currently no structured way to return an error — the match statement either panics or the code is non-exhaustive. This makes it impossible to handle unsupported backends gracefully.

## Expected Behavior

- There should be a dedicated error variant for "this operation is not supported by the current database backend."
- The error should carry two pieces of information: the name of the backend that is unsupported, and the name of the operation/context where the failure occurred.
- The database backend type should expose a way to get its name as a static string, so it can be embedded in structured error values.
- Code that matches on the backend type should be able to add a catch-all arm that returns this structured error rather than panicking.

## Why This Matters

Library code and user-facing migration utilities need to handle all possible backend values safely. Without a proper error variant, unsupported backends cause panics rather than recoverable errors. Adding this error variant allows callers to handle the case gracefully and provide meaningful diagnostics about which backend was encountered and what operation it was attempting.
