I'm working on a Kafka broker issue where per-partition expiration metrics for delayed produce and remote list offset operations are never cleaned up when a broker stops being the leader for a partition.

*   DelayedProduceMetrics must expose a removePartitionMetrics(partition: TopicPartition) method that removes the per-partition ExpiresPerSec meter from the Yammer metrics registry for the given partition.

*   DelayedProduceMetrics.removePartitionMetrics must be idempotent: calling it for a TopicPartition that was never registered (i.e. recordExpiration was never called for it) must not throw any exception.

*   DelayedProduceMetrics.removePartitionMetrics must not affect the aggregate ExpiresPerSec meter — only the per-partition meter (identified by topic and partition tags) must be removed.

*   DelayedRemoteListOffsets must expose a public static removePartitionMetrics(TopicPartition partition) method that removes the partition's meter from the PARTITION_EXPIRATION_METERS map and deregisters it from the Yammer metrics registry.

*   DelayedRemoteListOffsets.removePartitionMetrics must be idempotent: calling it for a TopicPartition that was never registered must not throw any exception.

*   DelayedRemoteListOffsets.removePartitionMetrics must not affect the AGGREGATE_EXPIRATION_METER — the aggregate meter's count must remain unchanged after the call.

*   DelayedRemoteListOffsets.METRICS_GROUP must be a package-private (not private) static field so that code in the same package can call its newMeter method to create per-partition meters.

*   DelayedRemoteListOffsets.PARTITION_EXPIRATION_METERS must be a package-private static Map<TopicPartition, Meter> field that tracks per-partition expiration meters and supports concurrent access.

*   DelayedRemoteListOffsets.AGGREGATE_EXPIRATION_METER must be a package-private static Meter field representing the aggregate expiration rate.


*   Interface details: Type: Method (Scala object)
Name: removePartitionMetrics
Object: DelayedProduceMetrics
Location: core/src/main/scala/kafka/server/DelayedProduce.scala
Signature: def removePartitionMetrics(partition: TopicPartition): Unit
Description: Removes the per-partition ExpiresPerSec meter from the Yammer metrics registry for the given TopicPartition. Must be a no-op (no exception) if the partition was never registered.

---

Type: Method (Java static)
Name: removePartitionMetrics
Location: storage/src/main/java/org/apache/kafka/server/purgatory/DelayedRemoteListOffsets.java
Signature: public static void removePartitionMetrics(TopicPartition partition)
Description: Removes the given TopicPartition's expiration meter from the PARTITION_EXPIRATION_METERS map and deregisters it from the Yammer metrics registry. Must be a no-op (no exception) if the partition was never registered.

---

Type: Field (Java static)
Name: METRICS_GROUP
Location: storage/src/main/java/org/apache/kafka/server/purgatory/DelayedRemoteListOffsets.java
Signature: static final KafkaMetricsGroup METRICS_GROUP
Description: Package-private (not private) static field holding the KafkaMetricsGroup used to create and remove meters. Must be accessible from other classes in the same package (org.apache.kafka.server.purgatory). Tests use it to call newMeter("ExpiresPerSec", "requests", TimeUnit.SECONDS, Map<String,String>) directly.

---

Type: Field (Java static)
Name: PARTITION_EXPIRATION_METERS
Location: storage/src/main/java/org/apache/kafka/server/purgatory/DelayedRemoteListOffsets.java
Signature: static final Map<TopicPartition, Meter> PARTITION_EXPIRATION_METERS
Description: Package-private static ConcurrentHashMap tracking per-partition expiration meters. Must support concurrent access and be directly accessible from the same package.

---

Type: Field (Java static)
Name: AGGREGATE_EXPIRATION_METER
Location: storage/src/main/java/org/apache/kafka/server/purgatory/DelayedRemoteListOffsets.java
Signature: static final Meter AGGREGATE_EXPIRATION_METER
Description: Package-private static Meter representing the aggregate (topic-agnostic) expiration rate. Must remain unaffected by calls to removePartitionMetrics.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.