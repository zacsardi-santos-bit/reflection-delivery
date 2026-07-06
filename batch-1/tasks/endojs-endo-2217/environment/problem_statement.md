## Description

When two peers in the distributed daemon each attempt to connect to the other at the same time, there is no mechanism to arbitrate which connection should win. This leads to both connections being established simultaneously, causing resource leaks, inconsistent state, and failures when objects are passed between peers.

There are two related issues:

1. **Simultaneous connection race conditions**: If peer A initiates an outbound connection to peer B, and peer B also initiates an outbound connection to peer A at the same time, the daemon ends up with two connections open for the same peer pair. There is no logic to decide which one to keep and properly cancel the other. Cancellation also fails to propagate correctly to all related connection attempts.

2. **Object identity not preserved across remote round-trips**: When a capability (a remotable object) is sent from one node to a remote peer and then returned via a remote call, the receiving node does not recognize the returned value as the same object it originally sent. This breaks identity-based patterns where code checks whether two references to a capability are the same object.

## Expected Behavior

- A deterministic arbitration mechanism should resolve simultaneous outbound/inbound connection attempts between two peers, keeping exactly one connection based on the peers' relative identities.
- Cancellation of any connection in an entangled group should propagate to all related connection attempts for the same peer.
- After a connection is fully torn down, a new connection attempt should be able to establish a fresh connection.
- When one peer has already connected to another, the reverse connection should be reachable without requiring an additional explicit peer introduction.
- Sending a capability to a remote peer and receiving it back should result in the original object being recognized as identical to the returned value.

## Why This Matters

Without these fixes, multi-peer scenarios where peers connect to each other in both directions (common when sharing capabilities) fail unpredictably. The lack of identity preservation on round-trips breaks capability-based access patterns that rely on object equality.
