Implement the handling of unclean shutdowns for brokers in Kafka's KRaft controller when the eligible-leader-replica (ELR) feature is enabled. Ensure that brokers re-registering after an unclean shutdown are correctly removed from the ELR set and that partition leadership and metadata are managed accurately.

*   Create a `BrokersToElrs` class in `org.apache.kafka.controller`.
    *   Constructor must accept a `SnapshotRegistry`.
    *   Implement `update(Uuid topicId, int partitionId, int[] prevElr, int[] nextElr)` to maintain broker ID indices in ELR.
    *   Implement `partitionsWithBrokerInElr(int brokerId)` to return a `BrokersToIsrs.PartitionsOnReplicaIterator` for partitions with the broker in ELR.

*   Update `ClusterControlManager.Builder`:
    *   Add `setBrokerUncleanShutdownHandler(BrokerUncleanShutdownHandler)` method.
    *   Ensure `build()` throws a `RuntimeException` if no handler is set.

*   Modify `ClusterControlManager` to:
    *   Detect unclean shutdowns during broker re-registration.
    *   Invoke `BrokerUncleanShutdownHandler` to append records before adding new broker registration records.

*   Enhance `ReplicationControlManager`:
    *   Maintain a `BrokersToElrs` instance, updating it on partition info changes.
    *   Expose `brokersToElrs()` method to return the `BrokersToElrs` instance.
    *   Implement `handleBrokerUncleanShutdown(int brokerId, List<ApiMessageAndVersion> records)` to manage partition change records.
    *   Clean up `BrokersToElrs` index when a topic is deleted.
    *   Ensure re-registering brokers are removed from ELR without altering `lastKnownElr`.
    *   Elect the last remaining ELR member as partition leader upon unfencing, clearing ELR and `lastKnownElr`.

*   Update `QuorumController.Builder`:
    *   Add `setEligibleLeaderReplicasEnabled(boolean eligibleLeaderReplicasEnabled)` method.
    *   Ensure `QuorumControllerTestEnv` passes `bootstrapMetadata.metadataVersion().isElrSupported()` as the argument.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.