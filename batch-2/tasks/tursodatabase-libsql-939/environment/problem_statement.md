## Description

When a database operation fails due to a SQLite error (such as a constraint violation), the error returned to clients over the server's HTTP streaming protocol only contains a human-readable message. There is no machine-readable error code included in the response.

This makes it hard for client applications to programmatically detect and handle specific error categories. Right now, clients have to parse the error message string to figure out what kind of error occurred — for example, to distinguish a uniqueness constraint violation from a read-only error or a type mismatch.

## Expected Behavior

- Errors returned through the stream protocol should include a machine-readable error code alongside the human-readable message.
- For a UNIQUE constraint violation, the error code should identify the constraint error category, not just provide a message string.
- Client error representations should reflect this structured information, making the error category observable without string parsing.

## Why This Matters

Clients that want to handle specific error types — like retrying after a lock error or showing a user-friendly message for a constraint violation — currently cannot do so reliably. Adding a structured error code makes it possible to write robust, type-safe error handling on the client side.
