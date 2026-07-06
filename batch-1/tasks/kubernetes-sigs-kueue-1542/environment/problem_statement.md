## Description

When Kueue evicts a workload because its pods did not become ready within the configured timeout, that workload is re-queued for rescheduling. Currently, Kueue always uses the eviction time as the ordering key when placing the re-queued workload back into the scheduling queue. This causes the workload to appear "newer" than it actually is, potentially placing it behind workloads that were submitted after it was originally created but before it was evicted. As a result, a workload that was submitted early may end up waiting longer than necessary after being evicted.

## Expected Behavior

Operators should be able to configure which timestamp Kueue uses when re-ordering workloads that were evicted due to pods not becoming ready in time. Two strategies should be supported:

- **Eviction timestamp** (default): the existing behavior, where the eviction time is used for queue ordering. A recently-evicted workload is sorted as if it arrived at the time of eviction.
- **Creation timestamp**: the workload's original submission time is always used. An evicted workload retains its original priority position in the queue and is not penalized for the eviction.

This configuration should live within the existing pods-ready waiting settings, with the eviction-timestamp strategy as the default to preserve backward compatibility. When the creation-timestamp strategy is in effect, a workload submitted earlier than its competitors should regain its queue position after eviction and be re-admitted before those later-submitted workloads.

## Why This Matters

Workloads that are evicted due to transient pod readiness issues should not necessarily be penalized by losing their position in the scheduling queue. Providing this knob lets cluster operators tune scheduling fairness to match their operational needs.
