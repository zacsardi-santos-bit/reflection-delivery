## Description

The logging system has two reliability issues that cause panics in certain edge cases.

**Span expiration panic**: When a span is created from a context that has a timeout or deadline, and that deadline expires before the span is explicitly closed, the logging system panics. This is a real production scenario — operations are frequently given timeouts, and if the operation finishes or is cancelled before a span's done function is called, the system crashes instead of completing gracefully.

**Invalid log level panic**: The logging system panics when it encounters an unrecognized log level value internally, rather than handling it gracefully. This provides no useful recovery path.

## Expected Behavior

- Spans should complete cleanly and without panic even when the underlying context has expired or been cancelled before the span's done function is invoked.
- Logging calls (including debug and info level) on an expired or cancelled context should succeed without panicking.
- Invalid log level values should be handled gracefully rather than causing a crash.
- A benchmark helper should exist that creates a logger at info level so that debug-message-filtering performance can be measured — when debug messages are sent to an info-level logger, nothing should be written.

## Why This Matters

Panicking under normal operating conditions (timeouts are routine) is unacceptable in production. The system should be robust to context lifecycle events, and spans should not be sensitive to whether their parent context is still alive when they're finalized.
