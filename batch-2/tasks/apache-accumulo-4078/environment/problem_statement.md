## Description

Accumulo server processes — tablet servers, scan servers, and compactors — currently have no way to signal that they are running but receiving no meaningful work. A server in a resource group that is not assigned any tables or compaction jobs looks identical in monitoring to a busy server. This makes it impossible to automatically detect idle processes that are consuming cluster resources without contributing to workload.

Additionally, there is currently no mechanism to attach operator-defined labels to all metrics emitted by a cluster's server processes. In environments with multiple Accumulo clusters sharing the same monitoring infrastructure, this forces operators to rely on indirect means to distinguish which cluster a metric originated from.

## Expected Behavior

- Each server type (tablet server, scan server, compactor) should emit a metric after it has been continuously idle for a configurable duration. The idle threshold should be controlled via a new configuration property.
- Operators should be able to specify a set of custom key-value tags in configuration that get attached to every metric emitted by every server process in the cluster.
- All metrics should carry a tag identifying the type of server process that emitted them.
- The idle metric should not appear during normal active server operation; it is only expected when servers are genuinely idle.

## Why This Matters

These improvements allow monitoring systems to automatically surface idle or underutilized server processes and let operators label their Accumulo metrics so they can be distinguished across multiple clusters or environments in shared dashboards and alerting systems.
