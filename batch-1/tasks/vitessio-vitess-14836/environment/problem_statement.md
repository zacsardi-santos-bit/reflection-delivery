## Description

When a tablet starts up, it needs to verify that the database administration user has the required privileges before allowing privileged operations to proceed. Currently, this verification happens as a one-time blocking call during startup, but there is no mechanism to ensure that subsequent operations (query execution, replication management, etc.) actually wait for grant verification to complete before attempting to connect.

This means that in certain startup sequences — particularly during restore operations — privileged operations can potentially be invoked before the grant verification has finished, leading to permission errors.

## Expected Behavior

- The grant verification logic should be moved into the tablet manager itself, replacing the standalone function in the tablet server package.
- Once grant verification completes (or is skipped for externally managed tablets), a signal should be recorded so that any operations waiting on it can proceed.
- Any privileged operation should wait for the grant verification signal before proceeding. If the context expires while waiting, the operation should return an appropriate timeout/cancellation error.
- If the wait time is zero, or if the tablet is externally managed, the verification should be skipped and the signal should be recorded immediately.
- If the DBA user does not acquire the necessary privileges within the configured wait time, an error should be returned indicating the timeout duration.

## Why This Matters

Without this synchronization, race conditions during startup can cause operations to fail with confusing permission errors when the database administration user's grants haven't been applied yet. By gating operations on the grant verification signal, the system becomes more robust during the tablet initialization sequence.
