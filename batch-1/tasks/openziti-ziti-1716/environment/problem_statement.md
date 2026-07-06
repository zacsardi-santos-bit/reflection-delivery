## Description

In a multi-controller raft cluster, when two controllers dial each other simultaneously, there is a race condition where one controller's connection attempt can overwrite the other's existing peer connection without any error being reported. The peer connection operation currently returns nothing, so callers have no way to know whether a connection was successfully registered or whether it silently displaced an existing one.

This creates subtle bugs during cluster formation: a valid connection gets replaced, the old connection handler is left dangling, and the cluster may enter an inconsistent state that is hard to diagnose.

## Expected Behavior

- The operation that registers a newly connected peer should return an error value, giving the caller the ability to detect failure conditions (e.g., a connection for that peer address already exists).
- When a peer connection is accepted successfully (no duplicate), the operation should indicate success to the caller.
- The readonly state of the mesh should still be updated correctly based on peer version matching, independently of the error signaling.

## Why This Matters

Without error return values from the peer connection operation, race conditions during cluster formation are undetectable and can lead to inconsistent cluster state, orphaned connections, or incorrect routing decisions. Proper error propagation allows the connection establishment code to retry, close the duplicate, or log the conflict instead of silently proceeding with corrupted state.
