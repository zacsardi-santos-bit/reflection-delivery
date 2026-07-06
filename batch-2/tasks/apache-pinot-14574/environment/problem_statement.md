## Description

When using the multi-stage query engine in a distributed Pinot cluster, there is currently no mechanism to limit the number of multi-stage queries that can execute concurrently. Under high load, this can lead to resource exhaustion and degraded cluster performance for all users.

We need a throttling component that enforces a cluster-wide limit on concurrent multi-stage queries and distributes that limit proportionally across all active brokers, taking into account the number of servers available.

## Expected Behavior

- A cluster administrator can configure a maximum number of concurrent multi-stage queries for the entire cluster.
- Each broker automatically computes its own local quota from the cluster-wide limit proportionally (scaled by servers and split among brokers), with a minimum of 1 permit per broker.
- When the quota is exhausted, new query attempts block until a permit becomes available or a timeout elapses, at which point the attempt is rejected.
- Setting the limit to zero or a negative value disables throttling entirely — all queries proceed without any concurrency constraint.
- When brokers or servers join or leave the cluster, the per-broker quota is automatically recalculated and the permit counts adjusted (which may result in temporarily negative available permits if the quota decreases).
- When the cluster-level configuration is updated, the per-broker quota is recalculated and permits adjusted accordingly.
- The enabled/disabled state of the throttler is determined at startup and cannot be toggled at runtime: if throttling was enabled at startup, it stays enabled; if it was disabled, it stays disabled.

## Why This Matters

Without per-broker query concurrency limits, a burst of simultaneous multi-stage queries can overwhelm the cluster. This feature gives operators a straightforward knob to protect cluster resources while keeping the limit in sync with the actual cluster topology automatically.
