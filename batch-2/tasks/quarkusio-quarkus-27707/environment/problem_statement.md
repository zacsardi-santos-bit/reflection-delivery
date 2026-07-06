## Description

When using server-sent events with a broadcaster, the server is not properly notified when a client closes the connection. Registered close callbacks on the broadcaster are never invoked when a client disconnects, making it impossible for server-side code to react to client disconnections. Additionally, if a client disconnects and a broadcast is subsequently attempted, the broadcaster tries to send to the now-closed connection, which leads to unexpected error callbacks instead of silently skipping the closed sink.

## Expected Behavior

- When an SSE client disconnects, any close callbacks registered on the broadcaster should be invoked on the server side.
- When a broadcast is sent after a client has disconnected, the closed sink should be silently removed/skipped — no error callback should be triggered as a result of sending to a stale connection.
- When the broadcaster itself is closed, all registered sinks should also be closed.
- After a sink is closed, subsequent broadcast calls should not attempt to send events to that sink.

## Why This Matters

Server applications need to know when clients disconnect so they can clean up resources, update state, or stop generating events. Without proper close notification, the server has no reliable way to detect client disconnections. The spurious error callbacks from broadcasting to closed sinks also make it harder to distinguish real errors from expected client-disconnect scenarios.
