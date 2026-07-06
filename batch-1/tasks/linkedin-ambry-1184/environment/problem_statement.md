## Description

The adaptive operation tracker currently supports latency-based speculative request dispatch at two granularities: the entire datacenter, or per partition. This means that when deciding whether an inflight request is "past due" and a parallel speculative request should be sent to another replica, the tracker compares elapsed time against a shared latency distribution — either for all replicas in a datacenter, or for all replicas of a given partition.

This works well in homogeneous environments, but in practice certain individual storage nodes or individual disks may be consistently slower than others. When these finer-grained latency differences exist, the current approach cannot detect them independently: a slow disk mixed with fast disks will dilute the histogram and delay detection, while a healthy node may get incorrectly flagged if it shares a histogram with a slow peer.

## Expected Behavior

- The tracker scope configuration should support two new levels of granularity: individual data node and individual disk.
- When configured for node-level tracking, each data node maintains its own latency distribution. A request to a slow node is detected and a speculative request is issued based on that specific node's historical latency, not the shared datacenter or partition histogram.
- When configured for disk-level tracking, each disk maintains its own latency distribution. Multiple disks on the same host can have independent latency thresholds.
- The metrics infrastructure must expose the resource-level latency histogram maps so that different resource types (partition, node, disk) can all be used interchangeably as map keys.
- When the tracker operates at the datacenter level, these finer-grained maps must not be allocated.

## Why This Matters

Clusters with degraded nodes or slow disks benefit significantly from finer-grained latency tracking, because speculative requests can be dispatched sooner and more precisely for the affected resource, without impacting unrelated replicas.
