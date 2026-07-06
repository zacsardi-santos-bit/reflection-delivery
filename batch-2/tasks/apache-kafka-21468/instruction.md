I'm working on adding retention utilization metrics to Kafka's remote log management system.

*   The buildRetentionSizeData method must accept two new parameters in addition to the existing ones: localLogSegmentsSize (total size of local log segments in bytes, a long) inserted as the third argument (after onlyLocalLogSegmentsSize and before logEndOffset), and localRetentionBytes (the configured local retention size in bytes, a long) inserted as the sixth argument (after epochEntries and before fullCopyFinishedSegmentsSizeInBytes). The full updated signature is: buildRetentionSizeData(long retentionSize, long onlyLocalLogSegmentsSize, long localLogSegmentsSize, long logEndOffset, NavigableMap<Integer, Long> epochEntries, long localRetentionBytes, long fullCopyFinishedSegmentsSizeInBytes).

*   The buildRetentionSizeData method must update a RetentionSizeInPercent metric, calculated as ((onlyLocalLogSegmentsSize + totalRemoteSegmentsSize) * 100) / retentionSize, where totalRemoteSegmentsSize is the total size of all remote log segments. The result is stored as an integer (truncating division). When retentionSize is 0 or negative, the metric must be set to 0.

*   The buildRetentionSizeData method must update a LocalRetentionSizeInPercent metric, calculated as (localLogSegmentsSize * 100) / localRetentionBytes. The result is stored as an integer. When localRetentionBytes is 0 or negative, the metric must be set to 0.

*   When retentionSize is negative (i.e., retention is disabled), buildRetentionSizeData must return Optional.empty() immediately without updating any metrics.

*   RLMExpirationTask must expose a registerMetrics() method that registers the RetentionSizeInPercent and LocalRetentionSizeInPercent metrics for JMX exposure. The JMX metric names must follow the patterns: 'name=RetentionSizeInPercent,partition=<partition>,topic=<topic>' and 'name=LocalRetentionSizeInPercent,partition=<partition>,topic=<topic>'.

*   RLMExpirationTask must expose retentionSizeInPercent() and localRetentionSizeInPercent() accessor methods that return the current int values of the respective metrics.

*   When cancel() is called on RLMExpirationTask, the RetentionSizeInPercent and LocalRetentionSizeInPercent JMX metrics must be deregistered, and both retentionSizeInPercent() and localRetentionSizeInPercent() must return 0 afterwards.

*   When retentionSize equals 0 and localRetentionBytes equals 0, both RetentionSizeInPercent and LocalRetentionSizeInPercent must be 0.


*   Interface details: Type: Method
Name: buildRetentionSizeData
Location: storage/src/main/java/org/apache/kafka/server/log/remote/storage/RemoteLogManager.java
Signature: buildRetentionSizeData(long retentionSize, long onlyLocalLogSegmentsSize, long localLogSegmentsSize, long logEndOffset, NavigableMap<Integer, Long> epochEntries, long localRetentionBytes, long fullCopyFinishedSegmentsSizeInBytes) -> Optional<RemoteLogManager.RetentionSizeData>
Description: Inner method of RLMExpirationTask. Builds retention size data for remote log expiration. Now takes two additional parameters compared to the original: localLogSegmentsSize (total size of local log segments in bytes) inserted as the third argument (after onlyLocalLogSegmentsSize and before logEndOffset), and localRetentionBytes (configured local log retention size in bytes) inserted as the sixth argument (after epochEntries and before fullCopyFinishedSegmentsSizeInBytes). Updates RetentionSizeInPercent and LocalRetentionSizeInPercent metrics during execution. Returns Optional.empty() immediately when retentionSize is negative.

Type: Method
Name: registerMetrics
Location: storage/src/main/java/org/apache/kafka/server/log/remote/storage/RemoteLogManager.java
Signature: registerMetrics() -> void
Description: Method on RLMExpirationTask inner class. Registers the RetentionSizeInPercent and LocalRetentionSizeInPercent metrics for JMX exposure. Must be called before the metrics are available via JMX. Visible for testing.

Type: Method
Name: retentionSizeInPercent
Location: storage/src/main/java/org/apache/kafka/server/log/remote/storage/RemoteLogManager.java
Signature: retentionSizeInPercent() -> int
Description: Accessor on RLMExpirationTask. Returns the current value of the RetentionSizeInPercent metric as an integer percentage. Returns 0 after cancel() has been called.

Type: Method
Name: localRetentionSizeInPercent
Location: storage/src/main/java/org/apache/kafka/server/log/remote/storage/RemoteLogManager.java
Signature: localRetentionSizeInPercent() -> int
Description: Accessor on RLMExpirationTask. Returns the current value of the LocalRetentionSizeInPercent metric as an integer percentage. Returns 0 after cancel() has been called.

Type: Method (existing, modified behavior)
Name: cancel
Location: storage/src/main/java/org/apache/kafka/server/log/remote/storage/RemoteLogManager.java
Signature: cancel() -> void
Description: Method on RLMExpirationTask. In addition to its existing behavior, must deregister the RetentionSizeInPercent and LocalRetentionSizeInPercent JMX metrics and reset both metric values to 0.

Type: Inner Class
Name: RLMExpirationTask
Location: storage/src/main/java/org/apache/kafka/server/log/remote/storage/RemoteLogManager.java
Description: Inner class of RemoteLogManager responsible for expiring remote log segments. Must expose RetentionSizeInPercent and LocalRetentionSizeInPercent as JMX metrics (registered via registerMetrics()) with metric names following the patterns: name=RetentionSizeInPercent,partition=<partition>,topic=<topic> and name=LocalRetentionSizeInPercent,partition=<partition>,topic=<topic>.

Type: Constants
Name: RETENTION_SIZE_IN_PERCENT_METRIC, LOCAL_RETENTION_SIZE_IN_PERCENT_METRIC
Location: storage/api/src/main/java/org/apache/kafka/server/log/remote/storage/RemoteStorageMetrics.java
Description: Public static final MetricName constants for the new percentage metrics. RETENTION_SIZE_IN_PERCENT_METRIC has name "RetentionSizeInPercent" and LOCAL_RETENTION_SIZE_IN_PERCENT_METRIC has name "LocalRetentionSizeInPercent". Both belong to the "kafka.log.remote"/"RemoteLogManager" metric group.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.