Implement a new module to manage peer connections in a distributed daemon, resolving simultaneous connection attempts and preserving object identity across remote interactions. Ensure that the connection manager handles race conditions, propagates cancellations, and supports bidirectional communication with minimal introductions.

*   Export the `makeRemoteControlProvider` function from `packages/daemon/src/remote-control.js`.
    *   Accept a single string argument representing the local node's identity.
    *   Return a function that, when called with a remote peer's string identity, returns a `RemoteControl` object.

*   Implement the `RemoteControl` object with the following methods:
    *   `connect(getGateway, cancel, cancelled, dispose?)`
        *   Accept up to four arguments: a gateway factory function, a cancel callback, a cancelled promise, and an optional dispose callback.
        *   Call `getGateway()` exactly once from an unconnected state and return the resulting gateway directly.
        *   Return the existing gateway if called while a connection is already active.
        *   Handle race conditions using lexicographic comparison of identity strings:
            *   If `localId > remoteId`, the outbound connection wins; cancel the inbound connection.
            *   If `localId < remoteId`, the inbound connection wins; cancel the outbound connection.
        *   Ensure cancellation propagates across all entangled connections.
        *   After cancellation and dispose callback invocation, reset to an unconnected state for fresh connections.

    *   `accept(gateway, cancel, cancelled, dispose?)`
        *   Accept a gateway object, a cancel function, a cancelled promise, and an optional dispose callback.
        *   Store the inbound gateway as the active connection if in an initial state.
        *   Handle race conditions similarly to `connect`, using lexicographic comparison:
            *   Cancel the inbound connection if an outbound connection is active and `localId > remoteId`.
            *   Switch to the inbound connection if `localId < remoteId`.
        *   Reject subsequent accepts if a connection is already active, entangling cancellations.
        *   Propagate cancellations across entangled connections.

*   Ensure a single unidirectional peer introduction suffices for bidirectional communication.
*   Preserve object identity across remote round-trips, ensuring returned capabilities are strictly identical to the originals.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.