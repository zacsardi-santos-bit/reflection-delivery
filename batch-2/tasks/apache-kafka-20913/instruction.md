I'm working on Kafka's tiered storage feature and want to add support for delaying when log segments get uploaded to remote storage.

*   TopicConfig must define new string constants REMOTE_COPY_LAG_MS_CONFIG (value: "remote.copy.lag.ms") and REMOTE_COPY_LAG_BYTES_CONFIG (value: "remote.copy.lag.bytes"). Both configs must accept values >= -1; values below -1 (such as -2) and non-numeric values must be rejected as invalid at configuration parse time.

*   RemoteLogManagerConfig must define new string constants LOG_REMOTE_COPY_LAG_MS_PROP (value: "log.remote.copy.lag.ms") and LOG_REMOTE_COPY_LAG_BYTES_PROP (value: "log.remote.copy.lag.bytes"). Both configs must accept values >= -1; values below -1 (such as -2) and non-numeric values must be rejected as invalid.

*   RemoteLogManagerConfig must expose methods logRemoteCopyLagMs() and logRemoteCopyLagBytes() that return the configured long values from LOG_REMOTE_COPY_LAG_MS_PROP and LOG_REMOTE_COPY_LAG_BYTES_PROP respectively. These values must be dynamically accessible via the broker's remoteLogManagerConfig.

*   LogConfig must define long constants DEFAULT_REMOTE_COPY_LAG_MS = 0 and DEFAULT_REMOTE_COPY_LAG_BYTES = 0.

*   LogConfig must expose methods remoteCopyLagMs() and remoteCopyLagBytes(): when the raw configured value is -1, each method returns the corresponding local retention value (localRetentionMs() or localRetentionBytes()); otherwise it returns the configured value.

*   LogConfig.validate() must throw ConfigException when REMOTE_COPY_LAG_MS_CONFIG is set to a positive value that exceeds the effective local retention ms. The exception message must contain the string "remote.copy.lag.ms". Values of 0 or -1 must not trigger this exception.

*   LogConfig.validate() must throw ConfigException when REMOTE_COPY_LAG_BYTES_CONFIG is set to a positive value that exceeds the effective local retention bytes. The exception message must contain the string "remote.copy.lag.bytes". Values of 0 or -1 must not trigger this exception.

*   Dynamic broker config validation must throw ConfigException when LOG_REMOTE_COPY_LAG_MS_PROP is positive and exceeds the effective local retention ms, and likewise when LOG_REMOTE_COPY_LAG_BYTES_PROP is positive and exceeds effective local retention bytes. This validation must apply for both per-broker and default (cluster-level) config updates.

*   RLMCopyTask.candidateLogSegments(log, fromOffset, lastStableOffset) must apply copy lag filtering using log.config().remoteCopyLagMs() and log.config().remoteCopyLagBytes(). When either resolved value equals 0, all non-active segments up to the last stable offset are included without delay (immediate upload). When neither is configured (defaults of 0), the same behavior applies.

*   When remoteCopyLagMs() > 0, a segment is eligible for upload if its age (currentTimeMs - largestTimestamp) is >= copyLagMs. If reading largestTimestamp throws IOException, the segment is treated as eligible. If the timestamp is in the future (segment age < 0), the segment is also treated as eligible.

*   When remoteCopyLagBytes() > 0, a segment is eligible for upload if the size lag (total log size - cumulative size of segments considered so far) is >= copyLagBytes.

*   When both remoteCopyLagMs() > 0 and remoteCopyLagBytes() > 0, a segment is eligible if EITHER the time-based condition OR the size-based condition is satisfied (OR logic).

*   When a segment is not eligible for upload (both applicable conditions are not met), candidateLogSegments() stops immediately and returns no further segments.

*   When both resolved copy lag values are <= 0 (i.e., both time and size checks are effectively unlimited, such as both local retentions being -1), no segments are returned.

*   RemoteLogManager.EnrichedLogSegment must be constructible as new EnrichedLogSegment(LogSegment segment, long nextOffset) and must support equality comparison.


*   Interface details: Type: Constant
Name: REMOTE_COPY_LAG_MS_CONFIG
Location: clients/src/main/java/org/apache/kafka/common/config/TopicConfig.java
Signature: public static final String REMOTE_COPY_LAG_MS_CONFIG = "remote.copy.lag.ms"
Description: Topic-level config key for time-based remote copy lag. Valid values: >= -1 (long). Values below -1 and non-numeric are invalid.

Type: Constant
Name: REMOTE_COPY_LAG_BYTES_CONFIG
Location: clients/src/main/java/org/apache/kafka/common/config/TopicConfig.java
Signature: public static final String REMOTE_COPY_LAG_BYTES_CONFIG = "remote.copy.lag.bytes"
Description: Topic-level config key for size-based remote copy lag. Valid values: >= -1 (long). Values below -1 and non-numeric are invalid.

Type: Constant
Name: LOG_REMOTE_COPY_LAG_MS_PROP
Location: storage/src/main/java/org/apache/kafka/server/log/remote/storage/RemoteLogManagerConfig.java
Signature: public static final String LOG_REMOTE_COPY_LAG_MS_PROP = "log.remote.copy.lag.ms"
Description: Broker-level config key for time-based remote copy lag. Valid values: >= -1 (long). Values below -1 and non-numeric are invalid.

Type: Constant
Name: LOG_REMOTE_COPY_LAG_BYTES_PROP
Location: storage/src/main/java/org/apache/kafka/server/log/remote/storage/RemoteLogManagerConfig.java
Signature: public static final String LOG_REMOTE_COPY_LAG_BYTES_PROP = "log.remote.copy.lag.bytes"
Description: Broker-level config key for size-based remote copy lag. Valid values: >= -1 (long). Values below -1 and non-numeric are invalid.

Type: Method
Name: logRemoteCopyLagMs
Location: storage/src/main/java/org/apache/kafka/server/log/remote/storage/RemoteLogManagerConfig.java
Signature: public long logRemoteCopyLagMs()
Description: Returns the configured value of LOG_REMOTE_COPY_LAG_MS_PROP as a long.

Type: Method
Name: logRemoteCopyLagBytes
Location: storage/src/main/java/org/apache/kafka/server/log/remote/storage/RemoteLogManagerConfig.java
Signature: public long logRemoteCopyLagBytes()
Description: Returns the configured value of LOG_REMOTE_COPY_LAG_BYTES_PROP as a long.

Type: Constant
Name: DEFAULT_REMOTE_COPY_LAG_MS
Location: storage/src/main/java/org/apache/kafka/storage/internals/log/LogConfig.java
Signature: public static final long DEFAULT_REMOTE_COPY_LAG_MS = 0
Description: Default value for the time-based remote copy lag (0 means no delay).

Type: Constant
Name: DEFAULT_REMOTE_COPY_LAG_BYTES
Location: storage/src/main/java/org/apache/kafka/storage/internals/log/LogConfig.java
Signature: public static final long DEFAULT_REMOTE_COPY_LAG_BYTES = 0
Description: Default value for the size-based remote copy lag (0 means no delay).

Type: Method
Name: remoteCopyLagMs
Location: storage/src/main/java/org/apache/kafka/storage/internals/log/LogConfig.java
Signature: public long remoteCopyLagMs()
Description: Returns the effective time-based remote copy lag in milliseconds. If the raw configured value is -1, returns localRetentionMs(); otherwise returns the configured value.

Type: Method
Name: remoteCopyLagBytes
Location: storage/src/main/java/org/apache/kafka/storage/internals/log/LogConfig.java
Signature: public long remoteCopyLagBytes()
Description: Returns the effective size-based remote copy lag in bytes. If the raw configured value is -1, returns localRetentionBytes(); otherwise returns the configured value.

Type: Method
Name: candidateLogSegments
Location: storage/src/main/java/org/apache/kafka/server/log/remote/storage/RemoteLogManager.java
Signature: List<RemoteLogManager.EnrichedLogSegment> candidateLogSegments(UnifiedLog log, long fromOffset, long lastStableOffset)
Description: Instance method on the inner class RemoteLogManager.RLMCopyTask. Returns the list of non-active log segments up to lastStableOffset that are eligible for upload to remote storage, filtered by the copy lag configuration from log.config().

Type: Class
Name: EnrichedLogSegment
Location: storage/src/main/java/org/apache/kafka/server/log/remote/storage/RemoteLogManager.java
Description: Inner class (or record) of RemoteLogManager representing a log segment with its next segment's base offset. Constructor: EnrichedLogSegment(LogSegment segment, long nextOffset). Must support equality comparison.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.