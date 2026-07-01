## Description

When Kafka Streams rebalances and tasks need to move between clients to achieve a more balanced assignment, the system currently always schedules those moves as warmup replicas — even when the destination client already has fully up-to-date state for the task in question. This means that a client which is completely caught up (either because it was the previous host or because it is within the acceptable lag threshold) still has to go through a warmup phase before it can take over active processing, causing unnecessary rebalancing delay.

## Expected Behavior

- The system should be able to identify, for each stateful task, which clients are already fully caught up.
- A client is considered caught up if it was the previous active host of the task or if its state lag is within the acceptable recovery threshold (effectively zero lag).
- When a task needs to be moved to a client that is already caught up, the reassignment should happen immediately — the task should be directly transferred without scheduling a warmup replica.
- Immediate movements should not count against the warmup replica limit.
- Tasks for which no clients are caught up continue to be treated as warmup movements as before.

## Why This Matters

Rebalancing is faster when the system can skip unnecessary warmup phases for clients that already hold current state. Avoiding redundant warmup work reduces end-to-end rebalancing latency and improves the responsiveness of the stream processing cluster when the topology changes.
