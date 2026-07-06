## Description

When a Kafka broker stops being the leader for a topic partition — due to partition deletion or a leadership transfer — per-partition expiration metrics registered during delayed produce and remote list offset operations are never cleaned up. These stale metrics accumulate in the broker's metrics registry indefinitely, growing without bound on long-running brokers with frequent partition reassignments or deletions.

## Expected Behavior

- A method should exist to explicitly remove per-partition expiration metrics for delayed produce operations when a partition is no longer served by the broker.
- A corresponding method should exist for remote list offset delayed operations, removing the partition's per-partition metric from both the internal tracking map and the underlying metrics registry.
- Both cleanup methods should be safe to call even if no metric was ever recorded for the given partition — no exception should be thrown.
- Cleaning up per-partition metrics must not affect aggregate-level metrics that track overall broker-wide expiration rates.

## Why This Matters

Without this cleanup, brokers that handle many partition reassignments or deletions will accumulate a growing set of stale per-partition metrics in their registry. This wastes memory and makes monitoring dashboards harder to interpret. With the cleanup in place, the metrics registry accurately reflects only the partitions currently managed by the broker.
