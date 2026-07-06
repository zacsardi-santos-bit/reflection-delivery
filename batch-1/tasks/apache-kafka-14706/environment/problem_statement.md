## Description

When Kafka's controller operates with the eligible-leader-replica (ELR) feature enabled, a broker that crashes and comes back online without a clean shutdown is not properly handled. Currently, the controller has no mechanism to detect this unclean restart scenario and take corrective action on the ELR set. As a result, a crashed broker may remain listed as eligible to become a partition leader even though its data could be stale, which can compromise recovery correctness.

## Expected Behavior

- When a broker re-registers with the controller in a way that indicates it did not shut down cleanly, the controller should remove that broker from the eligible-leader set for all partitions it was tracking.
- If the broker was the last member of a partition's in-sync replica set before crashing, the controller should record it as the last known leader for that partition so recovery can reference this information.
- The last known leader information should be preserved even if that broker also undergoes an unclean re-registration.
- When a topic is deleted, all eligible-leader bookkeeping for that topic's partitions should be cleaned up so no stale entries remain.
- When the sole remaining eligible-leader-replica for a partition successfully comes back online, it should be elected as the partition leader and all ELR tracking for that partition should be cleared.

## Why This Matters

Without this handling, the eligible-leader-replica data structures accumulate stale state after broker failures, potentially allowing brokers with out-of-date data to be considered for leadership. Proper unclean-shutdown detection ensures the controller's partition metadata stays accurate and recovery proceeds correctly under failure scenarios.
