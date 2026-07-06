## Description

When a QUIC stream is shut down due to a connection-level close, the shutdown event delivered to the application contains incomplete and inaccurate information about how the connection was closed. Specifically:

- The event payload has a field labeled as "peer-initiated shutdown" that is actually set based on whether the application layer closed the connection — the naming is misleading and doesn't reflect the true semantics.
- There is no way for the stream handler to determine whether the connection was closed from the remote side (i.e., by the peer), which is critical for proper error handling and diagnostics.

## Expected Behavior

- The connection-shutdown metadata in the stream shutdown-complete event should accurately indicate whether the connection was closed by the application layer (as opposed to the transport layer), via a correctly named field.
- A new field should be added indicating whether the connection was closed from the remote side (peer), allowing stream handlers to distinguish between local and remote connection closure.
- When a stream is aborted at the stream level (not a connection-level shutdown), the connection-shutdown fields in the event should all be zero/false, confirming they are independent from stream-level abort events.
- When the connection is shut down and stream handlers inspect the shutdown-complete event, the connection-closed-by-app and closed-remotely fields must correctly reflect the actual connection close cause, consistent with what the connection object itself reports.

## Why This Matters

Stream shutdown handlers often need to decide how to recover, log, or report errors. Without knowing whether the connection was closed by the application or the transport, and whether the closure came from the local side or the remote peer, applications cannot correctly distinguish between expected closures and unexpected errors — leading to incorrect behavior or poor diagnostics.
