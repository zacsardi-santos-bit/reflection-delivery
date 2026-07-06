I'm working on adding rack awareness to the Kafka producer's built-in partitioner.

*   The BuiltInPartitioner constructor must accept two additional parameters after stickyBatchSize: a boolean rackAware flag and a String rack identifier. The constructor must still throw IllegalArgumentException when stickyBatchSize is 0 or less.

*   When rackAware is true and a rack identifier is provided, BuiltInPartitioner must prefer partitions whose leader broker is in the same rack as the configured rack identifier during sticky partition selection.

*   When rackAware is true but all partitions in the local rack are offline (no available leader in that rack), BuiltInPartitioner must fall back to selecting from partitions in other racks.

*   When rack-local partitions become available again after having been offline, BuiltInPartitioner must resume preferring those local-rack partitions over non-local ones.

*   When broker nodes do not carry rack information (rack is null or empty on the Node), BuiltInPartitioner must treat all partitions equally regardless of the rackAware setting, cycling through all available partitions.

*   The updatePartitionLoadStats method must accept a String[] partitionRacks parameter (inserted between partitionIds and length) that records the rack of each partition's leader.

*   When rackAware is true and rack-annotated load stats are provided, adaptive partitioning must confine partition selection to rack-local partitions only. The loadStatsInThisRackRangeEnd() method must return the end of the cumulative frequency range that covers only the rack-local partitions.

*   When rackAware is true and all rack-local partitions are offline (absent from the load stats), loadStatsInThisRackRangeEnd() must return the full range end, and adaptive partitioning must select from all available partitions.

*   The RecordAccumulator.PartitionerConfig constructor must accept two additional parameters: a boolean rackAware flag and a String rack identifier, appended after the existing partitionAvailabilityTimeoutMs parameter.

*   The RecordAccumulator.createBuiltInPartitioner method must accept boolean rackAware and String rack parameters in addition to its existing parameters, and must pass them through to the BuiltInPartitioner constructor.


*   Interface details: Type: Class
Name: BuiltInPartitioner
Location: clients/src/main/java/org/apache/kafka/clients/producer/internals/BuiltInPartitioner.java
Description: Built-in producer partitioner that supports rack-aware partition selection.
Signature: BuiltInPartitioner(LogContext logContext, String topic, int stickyBatchSize, boolean rackAware, String rack)
Notes: Constructor must throw IllegalArgumentException when stickyBatchSize <= 0.

Type: Method
Name: updatePartitionLoadStats
Location: clients/src/main/java/org/apache/kafka/clients/producer/internals/BuiltInPartitioner.java
Signature: updatePartitionLoadStats(int[] queueSizes, int[] partitionIds, String[] partitionRacks, int length) -> void
Description: Updates the partition load statistics with queue sizes, partition IDs, their corresponding rack assignments, and the number of entries.

Type: Method
Name: loadStatsInThisRackRangeEnd
Location: clients/src/main/java/org/apache/kafka/clients/producer/internals/BuiltInPartitioner.java
Signature: loadStatsInThisRackRangeEnd() -> int
Description: Returns the end index (exclusive) of the rack-local partition range within the cumulative frequency table used for adaptive load-based partitioning. When rack-awareness is disabled or no local-rack partitions are available, returns the full table range end.

Type: Class
Name: RecordAccumulator.PartitionerConfig
Location: clients/src/main/java/org/apache/kafka/clients/producer/internals/RecordAccumulator.java
Description: Configuration for the built-in partitioner within the record accumulator.
Signature: PartitionerConfig(boolean adaptivePartitioningEnabled, int partitionAvailabilityTimeoutMs, boolean rackAware, String rack)

Type: Method
Name: createBuiltInPartitioner
Location: clients/src/main/java/org/apache/kafka/clients/producer/internals/RecordAccumulator.java
Signature: createBuiltInPartitioner(LogContext logContext, String topic, int stickyBatchSize, boolean rackAware, String rack) -> BuiltInPartitioner
Description: Factory method for creating a BuiltInPartitioner instance; must pass the rackAware flag and rack identifier through to the BuiltInPartitioner constructor.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.