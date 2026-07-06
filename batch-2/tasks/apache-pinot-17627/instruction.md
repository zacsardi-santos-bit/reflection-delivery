I'm working on the segment reload functionality in Apache Pinot and need to add time-range filtering support.

*   SegmentReloadMessage must support a 4-parameter constructor accepting an explicit reload job ID. When the job ID is non-null, getReloadJobId() must return that provided ID. When constructed with the 2-parameter constructor (no job ID), getReloadJobId() must return getMsgId() as the default.

*   Constructing a SegmentReloadMessage from a raw Helix message whose subtype is not the expected reload subtype must throw an IllegalArgumentException.

*   A new constant SEGMENT_RELOAD_JOB_INSTANCE_TO_SEGMENTS_MAP with value "instanceToSegmentsMap" must be added to CommonConstants.ControllerJob (in pinot-spi/src/main/java/org/apache/pinot/spi/utils/CommonConstants.java).

*   PinotControllerJobMetadataDto must expose a nullable instanceToSegmentsMap field via getInstanceToSegmentsMap() -> String and a fluent setter setInstanceToSegmentsMap(String) -> PinotControllerJobMetadataDto.

*   PinotHelixResourceManager.addNewReloadSegmentJob (6-param: tableNameWithType, segmentNames, instanceName, jobId, jobSubmissionTimeMs, numMessagesSent) must store all standard metadata keys (JOB_ID, TABLE_NAME_WITH_TYPE, JOB_TYPE, SUBMISSION_TIME_MS, MESSAGE_COUNT, SEGMENT_RELOAD_JOB_SEGMENT_NAME) and store SEGMENT_RELOAD_JOB_INSTANCE_NAME only when instanceName is non-null. The job must be submitted to addControllerJobToZK with ControllerJobTypes.RELOAD_SEGMENT.

*   PinotHelixResourceManager.addNewReloadSegmentJob must have a 7-parameter overload adding an optional instanceToSegmentsMapJson string. When non-null, the value is stored in job metadata under the SEGMENT_RELOAD_JOB_INSTANCE_TO_SEGMENTS_MAP key. When null, that metadata key is absent.

*   PinotHelixResourceManager.reloadSegments must be updated to accept a reloadJobId string parameter and return Map<String, Integer> (reload message count per instance). The reloadJobId is passed through to each SegmentReloadMessage created during dispatch.

*   PinotTableReloadService.reloadAllSegments must be updated to accept three additional parameters: nullable startTimestampStr (String), nullable endTimestampStr (String), and excludeOverlapping (boolean), for a total of 9 parameters. When at least one timestamp string is provided, the service uses time-range filtering mode.

*   In time-range mode: a missing start timestamp must use Long.MIN_VALUE as the effective start; a missing end timestamp must use Long.MAX_VALUE as the effective end. Non-parseable timestamp strings must result in a ControllerApplicationException with HTTP 400 status. A start timestamp that equals or exceeds the end timestamp must also result in HTTP 400.

*   In time-range mode, combining startTimestamp or endTimestamp with targetInstance must be rejected with HTTP 400. Combining startTimestamp or endTimestamp with instanceToSegmentsMap must be rejected with HTTP 400. Using excludeOverlapping=true without any timestamp must be rejected with HTTP 400.

*   In time-range mode, the service must call getSegmentsFor(tableNameWithType, true, startTimestamp, endTimestamp, excludeOverlapping) to obtain matching segments, then intersect this set with the full server-to-segments map to build a per-server dispatch map containing only the matching segments.

*   In time-range mode, when no matching segments exist across all tables or no reload messages are successfully sent, the service must throw a ControllerApplicationException with HTTP 404 status. When forceDownload is true and no table type is specified, the service must default to OFFLINE table type.

*   In time-range mode, a single ZK job entry is recorded per table. The segment names string stored in ZK must be the distinct, sorted list of reloaded segments joined with SegmentNameUtils.SEGMENT_NAME_SEPARATOR. The same job UUID must be used for both the reloadSegments call and the addNewReloadSegmentJob call.

*   The reload response is a JSON object keyed by table name (with type suffix). Each table entry must contain the string key "numMessagesSent" (total message count) and "reloadJobId" (the job UUID). When ZK storage of the job entry fails (addNewReloadSegmentJob returns false or throws), the entry must include "reloadJobMetaZKStorageStatus" = "FAILED".

*   When instanceToSegmentsMapInJson is provided without time-range parameters, the service must call the 7-parameter addNewReloadSegmentJob with the exact JSON of the instance-to-segments mapping.

*   PinotTableReloadStatusReporter.getServerToSegments(PinotControllerJobMetadataDto) must check whether instanceToSegmentsMap is set on the DTO. When it is non-null, the method must return the parsed map directly without querying the resource manager.

*   PinotTableReloadStatusReporter.getServerToSegments(String tableName, String segmentNamesString, String instanceName) must support multiple pipe-separated segments with a null instance name by deriving the per-server mapping through intersection of target segments with the full server-to-segments map.


*   Interface details: Type: Class
Name: SegmentReloadMessage
Location: pinot-common/src/main/java/org/apache/pinot/common/messages/SegmentReloadMessage.java
Description: Message class for triggering segment reloads on servers. Requires a new 4-parameter constructor that accepts an explicit reload job ID. When reloadJobId is non-null, getReloadJobId() must return the provided ID. When null (or when using the 2-parameter constructor), getReloadJobId() defaults to getMsgId().
Signature: SegmentReloadMessage(String tableNameWithType, @Nullable List<String> segmentNames, boolean forceDownload, @Nullable String reloadJobId)
Existing methods relied upon: getReloadJobId() -> String, getMsgId() -> String, shouldForceDownload() -> boolean, getSegmentList() -> List<String>

---

Type: Constant
Name: SEGMENT_RELOAD_JOB_INSTANCE_TO_SEGMENTS_MAP
Location: pinot-spi/src/main/java/org/apache/pinot/spi/utils/CommonConstants.java (inner class ControllerJob)
Description: New string constant for the instance-to-segments mapping metadata key used when storing reload job metadata in ZooKeeper. Value must be "instanceToSegmentsMap".

---

Type: Class
Name: PinotControllerJobMetadataDto
Location: pinot-common/src/main/java/org/apache/pinot/common/restlet/resources/PinotControllerJobMetadataDto.java
Description: DTO for controller job metadata. Requires a new nullable instanceToSegmentsMap field.
Signature:
  getInstanceToSegmentsMap() -> @Nullable String
  setInstanceToSegmentsMap(@Nullable String instanceToSegmentsMap) -> PinotControllerJobMetadataDto

---

Type: Method
Name: addNewReloadSegmentJob (7-parameter overload)
Location: pinot-controller/src/main/java/org/apache/pinot/controller/helix/core/PinotHelixResourceManager.java
Description: Overload of the existing 6-parameter method that additionally stores an optional JSON string mapping instances to their segment lists. When instanceToSegmentsMapJson is non-null, stores it under the SEGMENT_RELOAD_JOB_INSTANCE_TO_SEGMENTS_MAP metadata key. When null, that key is absent. The 6-parameter overload must delegate to this 7-parameter overload with null for the last argument.
Signature: addNewReloadSegmentJob(String tableNameWithType, String segmentNames, @Nullable String instanceName, String jobId, long jobSubmissionTimeMs, int numMessagesSent, @Nullable String instanceToSegmentsMapJson) -> boolean

---

Type: Method
Name: reloadSegments (updated signature)
Location: pinot-controller/src/main/java/org/apache/pinot/controller/helix/core/PinotHelixResourceManager.java
Description: Updated to accept a reloadJobId string (passed through to each SegmentReloadMessage) and to return Map<String, Integer> (message count per instance) instead of the previous return type. The reloadJobId is used when constructing each SegmentReloadMessage so that all messages in a single reload operation share the same job ID.
Signature: reloadSegments(String tableNameWithType, boolean forceDownload, Map<String, List<String>> instanceToSegmentsMap, String reloadJobId) -> Map<String, Integer>

---

Type: Method
Name: reloadAllSegments (updated signature)
Location: pinot-controller/src/main/java/org/apache/pinot/controller/services/PinotTableReloadService.java
Description: Extended with time-range parameters. When startTimestampStr or endTimestampStr is provided, uses time-range filtering mode. Missing start defaults to Long.MIN_VALUE; missing end defaults to Long.MAX_VALUE. Time-range mode is mutually exclusive with targetInstance and instanceToSegmentsMapInJson. excludeOverlapping=true without timestamps is rejected with HTTP 400. Non-parseable timestamps and start >= end are rejected with HTTP 400. Returns a SuccessResponse whose status string is a JSON object keyed by table name with type; each entry contains "numMessagesSent", "reloadJobId", and optionally "reloadJobMetaZKStorageStatus" = "FAILED" when ZK storage fails. 404 is returned when no segments match or no messages are sent.
Signature: reloadAllSegments(String tableName, @Nullable String tableTypeStr, boolean forceDownload, @Nullable String targetInstance, @Nullable String instanceToSegmentsMapInJson, @Nullable String startTimestampStr, @Nullable String endTimestampStr, boolean excludeOverlapping, HttpHeaders headers) -> SuccessResponse throws IOException

---

Type: Method
Name: getServerToSegments (updated)
Location: pinot-controller/src/main/java/org/apache/pinot/controller/services/PinotTableReloadStatusReporter.java
Description: When the job DTO's instanceToSegmentsMap field is non-null, parses and returns that map directly without consulting the PinotHelixResourceManager. The 3-parameter overload getServerToSegments(String tableName, String segmentNamesString, String instanceName) must also support multiple pipe-separated segments with a null instance name, returning the per-server mapping derived by intersecting target segments with the full server-to-segments map.
Signature: getServerToSegments(PinotControllerJobMetadataDto job) -> Map<String, List<String>>


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.