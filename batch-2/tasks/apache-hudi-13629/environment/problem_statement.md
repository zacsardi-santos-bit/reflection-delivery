## Description

When reading from Hudi's metadata table — for example, to look up record locations or secondary index entries — the system may create distributed in-memory data collections that are persisted for performance. If an exception occurs mid-operation, those persisted collections are never released, causing memory pressure and resource leaks in the cluster.

There is currently no mechanism to track or automatically clean up these cached distributed data objects when a metadata read fails partway through. Additionally, there is no way to release an entire dependency chain of cached collections at once; callers can only release the top-level object, leaving ancestor computations cached unnecessarily.

## Expected Behavior

- A cleanup manager should track distributed data objects registered by the current thread during a metadata read operation.
- If an exception occurs, the cleanup manager should automatically release all tracked data objects before re-throwing the exception.
- If the operation succeeds, tracked objects should **not** be eagerly released by the cleanup manager.
- Key metadata read operations (record index lookups and secondary index lookups) should use this cleanup manager so their intermediate cached data is freed on failure.
- Both pair-type and non-pair-type distributed data collections should support releasing themselves along with all their upstream dependency chain in one call.

## Why This Matters

Without cleanup on failure, long-running services or repeated index lookups that encounter errors will accumulate cached RDDs in Spark's memory, degrading performance and potentially causing out-of-memory failures. This change ensures that failures are handled gracefully without leaving memory leaks behind.
