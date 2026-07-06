## Description

When an admission check fails for a workload that has already been admitted and is actively using cluster resources, the system should not immediately terminate it the same way it would terminate a workload that was never admitted. Currently, the workload controller handles all admission check rejections identically — regardless of whether the workload is already running with reserved quota or was never started. This causes admitted workloads to be abruptly marked as done without going through the proper eviction lifecycle.

## Expected Behavior

- When an already-admitted workload (with quota reserved and in admitted state) has an admission check rejected, the system should first trigger eviction for that workload rather than immediately marking it as finished.
- During eviction, the workload's admitted status should remain unchanged — it should stay true until eviction is complete.
- After eviction completes, the workload should be marked as finished with a reason indicating the admission check rejection, and the admitted status should be cleared.
- For workloads that were never admitted (no quota reservation, not in admitted state), the existing behavior of directly marking as finished when checks are rejected should be preserved.

## Why This Matters

Bypassing eviction for admitted workloads with rejected checks prevents the normal cleanup lifecycle from running and can leave cluster state inconsistent. The eviction step allows other components to react to the workload being terminated before it is marked as done. This distinction between admitted and unadmitted workloads is important for maintaining correct system behavior.
