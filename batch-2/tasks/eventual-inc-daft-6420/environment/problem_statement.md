## Description

Daft currently supports a subscriber mechanism for observing query execution, but it does not ship with any built-in subscriber that persists these events to disk. As a result, developers have no convenient way to capture and review the full lifecycle of their queries — when they start, what their query plans look like, whether they succeed or fail, and how long each stage takes. This makes debugging and profiling queries harder than it needs to be.

## Expected Behavior

- A new event-logging subscriber should be available that writes structured, per-query event log files to a configurable directory on disk.
- Each query should get its own subdirectory containing a log file with one JSON event per line.
- The events written for a query should include at minimum: a session header event, a query-start event, the unoptimized query plan, and a query-end event that records the final status (indicating whether the query succeeded, failed, or was canceled) and the elapsed duration in milliseconds.
- When a query ends (for any reason), all internal timing state for that query should be cleaned up, while state for other concurrent queries remains untouched.
- Simple helper functions should allow users to enable and disable this event logging at any time, with the subscriber automatically attached to and detached from the Daft runtime context.
- Disabling event logging should mark the subscriber as closed and fully detach it from the context.

## Why This Matters

Having structured, on-disk query lifecycle events enables developers to review what happened during a query run without instrumenting their code. It is a foundation for diagnostics, performance tracking, and debugging.
