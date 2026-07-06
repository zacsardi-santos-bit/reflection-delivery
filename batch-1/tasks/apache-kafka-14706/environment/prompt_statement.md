I'm working on Kafka's KRaft controller and I need help implementing proper handling for brokers that undergo an unclean shutdown while the eligible-leader-replica feature is enabled.

Right now, when a broker crashes and re-registers with the controller under a new identity (indicating it did not shut down cleanly), the controller doesn't do anything about the eligible-leader-replica (ELR) state — it just leaves the broker in the ELR for any partitions it was tracking. This is a problem because a crashed broker might have stale data and should not remain eligible to become a leader.

I need the controller to detect this unclean-shutdown scenario during broker re-registration and respond by removing the broker from the ELR for all affected partitions. Additionally, if the broker was the last member of a partition's in-sync replica set at the time it crashed, it should be recorded as the last known leader for that partition so the cluster can track which node had the most recent data.

I also need a new data structure to maintain the reverse mapping from broker IDs to the partitions they appear in within the ELR set — similar to how the existing ISR tracking works — so the controller can efficiently look up which partitions need to be updated when a specific broker has an unclean restart.

On top of this, when a topic is deleted, all ELR-related tracking entries for that topic's partitions need to be cleaned up. And when the sole remaining eligible replica for a partition comes back online and unfences, it should be elected as the partition leader with the ELR and last-known-leader state fully cleared.

The builder for the cluster control manager component needs to accept a handler callback that will be invoked with the appropriate broker ID and record list whenever an unclean shutdown is detected, so that the replication control layer can generate the necessary partition change records in the same atomic operation.
