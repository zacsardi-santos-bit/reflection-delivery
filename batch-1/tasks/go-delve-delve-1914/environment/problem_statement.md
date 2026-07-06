## Description

The Delve DAP server currently rejects all requests asking for the list of active goroutines, treating it as an unsupported operation. This means that when a debugging client like VS Code initiates a debug session and asks which threads (goroutines) are running, it receives an error instead of a proper response. Listing threads is a core part of the Debug Adapter Protocol, and even minimal implementations are required to return at least one placeholder entry.

## Expected Behavior

- When the debugger is paused at a breakpoint with active goroutines, a threads request should return the real list of goroutines, each with a numeric ID and the name of the function currently executing.
- When goroutine information is not yet available (such as during early program startup), the server should return a single placeholder thread entry as required by the protocol specification.
- When the debugged program has already terminated, the server should return an empty threads list.
- Stack trace requests can remain unsupported for now and should still return an appropriate error response.

## Additional Fixes

- The debug client's request sequence counter should start at 1 instead of 0, matching VS Code's actual numbering behavior. This affects how response sequence numbers are validated.
- The client should not log raw protocol messages to standard output during testing.

## Why This Matters

Without goroutine listing support, debugging tools cannot show the user which goroutines are active when stopped at a breakpoint. This is essential for multi-goroutine debugging workflows and is expected by any DAP-compliant client.
