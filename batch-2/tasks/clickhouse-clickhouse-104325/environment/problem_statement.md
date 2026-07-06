## Description

ClickHouse's integration test suite includes a test that verifies correct cleanup of interrupted snapshot transfers to remote object storage. The test deliberately kills a node during a snapshot transfer, records which temporary objects are left behind in the bucket, and then restarts the node. A polling loop then waits for those specific temporary objects to be removed, which confirms that the node's startup cleanup logic is functioning correctly.

The test has been unreliable when the restarted node quickly re-engages with the cluster leader. If no new commits have been applied in the meantime, the leader retransmits the same snapshot, which creates a new temporary object whose bucket name matches one of the objects recorded from the interrupted transfer. Because the test tracks interrupted-transfer objects by name alone, it cannot tell the new in-flight object apart from the old orphaned one. It detects an object with a matching name still present and incorrectly reports a cleanup failure, even though the original interrupted-transfer object was already removed at startup.

## Expected Behavior

- When recording which temporary objects were left behind by the interrupted transfer, the test should capture both the object name and the last-modification timestamp for each object, rather than capturing the object name alone.
- The last-modification timestamp has sufficient precision and is strictly monotonically increasing across writes, making the combination of name and timestamp a reliable unique identifier for a specific object written at a specific time.
- The polling loop that waits for the interrupted-transfer objects to be removed should match against this combined identifier, so that a freshly created object with a coincidentally matching name but a different timestamp is not treated as the original interrupted-transfer object.
- The test should report success as soon as the original objects — identified by both name and timestamp — have disappeared, regardless of whether new in-flight objects with the same names appear after the node restarts.

## Why This Matters

Tracking by name alone is not a reliable identity for an object when the same name can be reused by a new write. The false-positive failure this causes makes the test environment appear broken when cleanup is actually working correctly. Using a combined name-and-timestamp identifier eliminates the race condition, making the test a trustworthy signal for the cleanup behavior it is designed to verify.
