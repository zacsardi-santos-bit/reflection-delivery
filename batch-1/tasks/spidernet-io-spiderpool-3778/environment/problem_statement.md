## Description

Spiderpool's garbage collection system can incorrectly reclaim IP addresses from the pool during rapid StatefulSet scaling operations, leading to IP address conflicts where the same IP is assigned to multiple pods simultaneously.

The root cause is that the endpoint cleanup logic is scattered across multiple code paths. When the GC needs to clean up a network endpoint record, it has to manually handle various states: whether the endpoint is already being deleted, whether it still has a finalizer blocking its deletion, or whether it has already been removed. This fragmentation leads to race conditions and incorrect GC decisions in rapid scale-up/scale-down scenarios.

## Expected Behavior

- A unified endpoint release operation should be available that handles all lifecycle scenarios safely:
  - If the endpoint no longer exists, the operation should succeed as a no-op
  - If the endpoint exists but has not yet been marked for deletion, it should be deleted
  - If the endpoint is already being deleted (has a deletion timestamp), its finalizer should be removed to allow the deletion to complete
- Errors during deletion or finalizer removal should be propagated to the caller
- The operation should be available as part of the workload endpoint manager's interface so other components (such as the GC scan-all path) can use it

## Why This Matters

Without this fix, rapid replica scaling of StatefulSet workloads can cause Spiderpool to mistakenly believe an IP address is no longer in use and reclaim it, only for it to be reassigned to a new pod — while the original pod is still running. This leads to IP conflicts in the cluster, which causes network communication failures and is difficult to detect and recover from manually.
