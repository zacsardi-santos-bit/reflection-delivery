## Description

The server currently has no way to accept a pre-created network listener from the caller — it always creates its own socket internally based on the configured address. This means advanced use cases like socket inheritance, zero-downtime restarts, or integration testing with a pre-allocated listener are impossible to implement cleanly.

Additionally, when the server fails to start (for example, because the configured address is syntactically invalid), the error is not reliably returned to the caller. This makes error handling brittle and forces callers to deal with panics or undetected failures.

## Expected Behavior

- Developers should be able to pass an externally created network listener when setting up the server, and the server should use that listener for accepting connections rather than creating a new one.
- If a custom listener is provided, any address configured separately should be ignored.
- The server startup methods should return a meaningful error to the caller whenever startup fails, including when the address is invalid.

## Why This Matters

Without the ability to inject a custom listener, use cases like socket hand-off between processes or test environments that rely on a specific pre-bound socket are blocked. And without proper error propagation from startup methods, callers cannot reliably detect or recover from startup failures.
