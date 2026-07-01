## Description

When a gRPC client connection is established over TLS, the root certificate authorities used to verify the server's identity are fixed at connection creation time. There is currently no mechanism to update those trusted root CAs on a live connection — they are baked into the static TLS credentials at dial time.

This becomes a problem in a distributed consensus cluster where nodes may join with different certificate authorities. When a new orderer node with a different TLS root CA is added to a running cluster, existing cluster members cannot verify its certificate, so the connection fails and cannot recover without a full teardown and restart.

## Expected Behavior

- It should be possible to provide a callback when creating a gRPC client connection. That callback receives the TLS configuration before each handshake and may update it — for example, by replacing the trusted root certificate pool.
- A connection created with an initially incorrect set of root CAs should not permanently fail; it should enter a temporary failure state.
- Once the callback is updated to return the correct root CAs and reconnection is triggered, the connection should automatically recover to a working state without being closed and reopened.
- Existing connection code that does not pass any callback must continue to work without modification.

## Why This Matters

Without dynamic TLS root CA support, adding a new consensus node with a different certificate authority to a live cluster requires restarting all cluster members. With dynamic support, root CAs can be updated in the channel configuration after the new node is added, and connections will recover automatically.
