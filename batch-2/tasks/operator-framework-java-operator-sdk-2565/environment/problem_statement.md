## Description

When a Kubernetes operator uses server-side apply to manage workloads, it encounters false positive "drift detected" signals that trigger unnecessary resource updates. This happens because Kubernetes stores resource quantities (like CPU and memory limits/requests) in its own canonical string format on the server, which may differ from how those values are expressed in the desired state. For example, a CPU limit of "2 cores" might be stored by the server as "2", while the operator configuration expresses it as "2000m" — even though these are exactly equivalent values. The current comparison logic treats these as mismatched, causing the operator to repeatedly try to reconcile resources that are already in the desired state.

## Expected Behavior

- When comparing actual and desired workload states, if a resource quantity in the actual state is numerically identical to the desired quantity but expressed in a different string format, this should be treated as a match (no update needed).
- This normalization should apply to both regular containers and init containers, covering both resource requests and limits.
- This behavior should work for all supported workload types — not just StatefulSets, but also ReplicaSets and DaemonSets.
- Genuine differences in resource quantities (i.e., numerically different values) must still be detected as mismatches.
- When the structures of the actual and desired workloads differ (mismatched container counts, different container names, missing resource specifications, or different numbers of resource keys), no normalization should be attempted — the comparison proceeds as normal.

## Why This Matters

Operators that manage workloads with resource requirements specified in standard unit formats often end up in an infinite reconciliation loop. Every reconciliation detects a "mismatch" that isn't really a mismatch, then applies an unnecessary update that Kubernetes stores in its canonical form, only for the next reconciliation to detect the same false difference. This fix eliminates that loop for the affected workload types.
