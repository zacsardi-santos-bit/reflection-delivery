Resolve the reliability issues in the logging system by ensuring that spans and log levels handle edge cases gracefully without causing panics. Implement a benchmark helper to test logger performance at the info level.

*   Implement the `newBenchInfoLogger` function in `src/internal/log/testing.go`:
    *   Create a logger at the info level, not the debug level.
    *   Attach the logger to a background context.
    *   Return the context and a pointer to an atomic int64 byte counter.
    *   Ensure debug messages sent to this logger do not increment the counter.

*   Ensure logging behavior when using different log levels:
    *   Debug logging on a context with an info-level logger should result in zero bytes written to the output.
    *   Debug logging on a context with a debug-level logger should result in bytes being written to the output.

*   Update the `SpanContext` function in `src/internal/log/span.go`:
    *   Prevent panics when the context's deadline expires before the `EndSpanFunc` is called.
    *   Ensure logging on the returned context succeeds even after expiration or cancellation.
    *   Ensure calling the done function after context expiration completes without panic.

*   Handle invalid log level values:
    *   Modify the logging system to handle unrecognized or out-of-range log level values gracefully.
    *   Remove any test cases that expected a panic for invalid log level values.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.