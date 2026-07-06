I'm working on fixing how the workload controller handles admission check failures for workloads that are already admitted and running. Right now, when any admission check is rejected, the controller immediately marks the workload as finished — but this is wrong for workloads that already have quota reserved and are in an admitted state. Those workloads should go through proper eviction first before being marked as done.

The correct behavior should be: if an admission check is rejected on a workload that is already admitted, the controller should set an eviction condition on the workload and wait for eviction to complete. The workload's admitted status should remain as-is during this eviction period — it should not be prematurely cleared. Only after eviction finishes should the workload be marked as done due to the rejected check.

For workloads that haven't been admitted yet (no quota reservation), the existing behavior of directly marking as finished on check rejection should stay unchanged.

Additionally, when a workload's quota reservation is cleared, the admitted condition should be recalculated to reflect the new state correctly.
