## Description

There is currently no way to configure a server-wide default result storage location in Prefect. Every flow and deployment that needs result storage must have it configured individually, which is cumbersome when you want all flows on a server to share the same storage backend. Administrators need a centralized way to set, read, and clear a default result storage block that applies server-wide.

## Expected Behavior

- Administrators can set a specific storage block as the server-wide default for result storage through the administration API.
- The current server default result storage configuration can be read back at any time. When no default is configured, the response indicates an unset state.
- The server default result storage can be cleared, after which the configuration returns to its unset state.
- Attempting to set a block that does not exist should be rejected with a "not found" error.
- Attempting to set a block that exists but is not suitable for result storage should be rejected with an appropriate validation error.
- These operations are available through both the asynchronous and synchronous Python clients.

## Why This Matters

Without a server-level default, teams must configure storage on every individual flow or deployment. A centralized default simplifies setup in environments where a single shared storage backend should apply to all flows, and allows administrators to change or clear the default without modifying each flow individually.
