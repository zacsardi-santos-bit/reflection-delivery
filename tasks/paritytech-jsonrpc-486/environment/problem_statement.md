## Description

The `#[rpc]` derive macro can define RPC trait methods as notifications — methods that return nothing (no `Result`, just unit) and do not expect a response from the server. However, the generated client code for notification methods is currently broken: it simply panics with `unimplemented!()` and returns nothing, making it impossible to send notifications from a generated client.

## Expected Behavior

- When a trait method is marked as an RPC notification (by returning nothing/unit), the derived client should generate a working method that actually sends the notification over the wire.
- The generated client notification method should return a future that resolves when the notification has been dispatched, so callers can chain on it or await it.
- The server's notification handler should be invoked with the correct parameters when a notification is sent from a generated client.
- The underlying client transport layer (`TypedClient` and `RawClient`) should provide a `notify` method capable of sending a fire-and-forget notification to the server.

## Why This Matters

Developers relying on the `#[rpc]` derive macro to generate clients cannot currently use any notification methods — the generated code crashes at runtime. This makes the feature essentially unusable for any trait that includes notification methods, breaking a core part of the JSON-RPC specification.
