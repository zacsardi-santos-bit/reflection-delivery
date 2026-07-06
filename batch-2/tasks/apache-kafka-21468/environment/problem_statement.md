## Description

When using tiered (remote) storage for Kafka topics, operators currently have no direct metric that shows how much of the configured retention capacity is actually being consumed. To understand utilization, they must manually compute ratios from raw byte counters, which is error-prone and inconvenient. There should be dedicated percentage-based metrics that make it easy to see at a glance how close a partition is to its retention limits.

## Expected Behavior

- A new metric should report the percentage of the total configured retention size that is currently being used, accounting for both local and remote log storage combined.
- A second new metric should report the percentage of the local-only retention limit that is being consumed by local log segments.
- Both metrics should be exposed via standard monitoring (JMX) on a per-partition basis.
- Metrics must be properly cleaned up (deregistered and reset to zero) when a partition is no longer being managed by the remote log expiration task.
- When retention is disabled (set to a negative value), the expiration method should return no result and the metrics should not be updated.
- When retention limits are configured as zero, both percentage metrics should report zero.

## Why This Matters

Without these metrics, it is difficult to detect when a partition is approaching or exceeding its retention capacity, especially when data is spread across both local and remote storage tiers. Percentage-based metrics provide immediate, intuitive visibility into retention utilization, enabling operators to take proactive action before issues arise.
