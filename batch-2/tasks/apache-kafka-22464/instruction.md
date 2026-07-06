I'm working on the Kafka Streams group coordinator and I need to add support for tracking topology description state across coordinator restarts.

*   The StreamsGroupDescribeResult class must be created in the org.apache.kafka.coordinator.group.streams package. Its constructor must accept a List of described groups and a Map<String, Integer> of stored-description-topology-epochs, and throw NullPointerException for either null argument.

*   StreamsGroupDescribeResult.describedGroups() must return the list passed at construction; the returned list must be immutable (mutation throws UnsupportedOperationException).

*   StreamsGroupDescribeResult.storedDescriptionTopologyEpochs() must return the map passed at construction; the returned map must be immutable (mutation throws UnsupportedOperationException).

*   StreamsGroupHeartbeatResult must be updated to accept a third constructor argument: an int named currentTopologyEpoch. The existing two-argument constructor behavior must be preserved through the three-argument form (passing -1 as the epoch is the default/no-epoch sentinel).

*   StreamsGroupHeartbeatResult.currentTopologyEpoch() must return the int value passed as the third constructor argument.

*   The currentTopologyEpoch field must be included in StreamsGroupHeartbeatResult equality comparison: two instances with the same response data and topics map but different epoch values must be unequal; two instances with all three fields identical must be equal.

*   StreamsGroupHeartbeatResult.creatableTopics() must return an immutable map (mutation attempts must throw UnsupportedOperationException).

*   StreamsGroupHeartbeatResult constructor must throw NullPointerException when the StreamsGroupHeartbeatResponseData argument is null.

*   StreamsCoordinatorRecordHelpers.newStreamsGroupMetadataRecord must be updated to accept two additional int parameters: storedDescriptionTopologyEpoch and failedDescriptionTopologyEpoch (appended after the existing assignmentConfigs parameter). The method must write these values into the corresponding fields on the StreamsGroupMetadataValue record.

*   The StreamsGroupMetadataValue message schema must be extended with two new int fields: storedDescriptionTopologyEpoch and failedDescriptionTopologyEpoch. These fields must be readable and writable (getters and setters generated).

*   StreamsGroup must expose int storedDescriptionTopologyEpoch() and int failedDescriptionTopologyEpoch() accessors, both defaulting to -1. It must also expose void setStoredDescriptionTopologyEpoch(int) and void setFailedDescriptionTopologyEpoch(int) mutators.

*   StreamsGroup must expose int currentTopologyEpoch() which returns the epoch of the currently set topology, or -1 if no topology has been set.

*   When a StreamsGroupMetadata record is replayed, the coordinator must read storedDescriptionTopologyEpoch and failedDescriptionTopologyEpoch from the record and apply them to the in-memory StreamsGroup via the setters. The latest replayed record's values take effect.

*   GroupMetadataManager.streamsGroupDescribe must be updated to return StreamsGroupDescribeResult instead of List<DescribedGroup>. The result's storedDescriptionTopologyEpochs map must contain an entry for each group that was successfully found, mapping its group ID to its storedDescriptionTopologyEpoch read at committedOffset. Groups that are not found must be omitted from the map.

*   GroupMetadataManager must provide a new method validateStreamsGroupMember(String groupId, String memberId, long committedOffset) that returns the StreamsGroupMember for the given member. It must throw GroupIdNotFoundException if the group does not exist at committedOffset, and UnknownMemberIdException if the group exists but the member does not exist at committedOffset. Uncommitted records (tombstones) beyond committedOffset must not affect the result.

*   GroupCoordinatorShard.streamsGroupDescribe must return StreamsGroupDescribeResult (delegating to GroupMetadataManager.streamsGroupDescribe).

*   GroupCoordinatorShard must provide a new void method validateStreamsGroupMember(String groupId, String memberId, long committedOffset) that delegates to GroupMetadataManager.validateStreamsGroupMember and propagates GroupIdNotFoundException and UnknownMemberIdException.


*   Interface details: Type: Class
Name: StreamsGroupDescribeResult
Location: group-coordinator/src/main/java/org/apache/kafka/coordinator/group/streams/StreamsGroupDescribeResult.java
Description: Immutable result object bundling the list of described groups with a per-group map of stored-description-topology-epochs. Both constructor arguments are required (null throws NullPointerException). Both returned collections are unmodifiable.
Signature: StreamsGroupDescribeResult(List<StreamsGroupDescribeResponseData.DescribedGroup> describedGroups, Map<String, Integer> storedDescriptionTopologyEpochs)
Methods:
  List<StreamsGroupDescribeResponseData.DescribedGroup> describedGroups()
  Map<String, Integer> storedDescriptionTopologyEpochs()

Type: Class
Name: StreamsGroupHeartbeatResult
Location: group-coordinator/src/main/java/org/apache/kafka/coordinator/group/streams/StreamsGroupHeartbeatResult.java
Description: Existing result class for streams group heartbeat. Must be updated to accept a third constructor parameter (int currentTopologyEpoch) and expose a corresponding accessor. The currentTopologyEpoch field must be part of equality. The creatableTopics() accessor must return an immutable map. Null StreamsGroupHeartbeatResponseData must throw NullPointerException. The sentinel value for "no topology" is -1.
Signature: StreamsGroupHeartbeatResult(StreamsGroupHeartbeatResponseData data, Map<String, ?> creatableTopics, int currentTopologyEpoch)
Methods:
  int currentTopologyEpoch()
  Map<String, ?> creatableTopics()

Type: Class
Name: StreamsCoordinatorRecordHelpers
Location: group-coordinator/src/main/java/org/apache/kafka/coordinator/group/streams/StreamsCoordinatorRecordHelpers.java
Description: Existing helper class for creating coordinator records. The newStreamsGroupMetadataRecord method must be updated to accept two additional int parameters for topology description epoch tracking.
Signature: static CoordinatorRecord newStreamsGroupMetadataRecord(String groupId, int epoch, long metadataHash, int validatedTopologyEpoch, Map<String, String> assignmentConfigs, int storedDescriptionTopologyEpoch, int failedDescriptionTopologyEpoch)

Type: Schema
Name: StreamsGroupMetadataValue
Location: group-coordinator/src/main/resources/common/message/StreamsGroupMetadataRecord.json (or equivalent Kafka message schema file)
Description: The generated StreamsGroupMetadataValue class must have two new int fields added to its schema: storedDescriptionTopologyEpoch and failedDescriptionTopologyEpoch. These fields must have corresponding setters (setStoredDescriptionTopologyEpoch, setFailedDescriptionTopologyEpoch) and getters (storedDescriptionTopologyEpoch, failedDescriptionTopologyEpoch).

Type: Class
Name: StreamsGroup
Location: group-coordinator/src/main/java/org/apache/kafka/coordinator/group/streams/StreamsGroup.java
Description: Existing streams group class. Must be extended with two new epoch fields (both defaulting to -1) and a method to read the current topology epoch.
New methods:
  int storedDescriptionTopologyEpoch()
  void setStoredDescriptionTopologyEpoch(int epoch)
  int failedDescriptionTopologyEpoch()
  void setFailedDescriptionTopologyEpoch(int epoch)
  int currentTopologyEpoch()  — returns the epoch of the currently set topology, or -1 if no topology is set

Type: Class
Name: GroupMetadataManager
Location: group-coordinator/src/main/java/org/apache/kafka/coordinator/group/GroupMetadataManager.java
Description: Existing manager class. Two changes are required: (1) streamsGroupDescribe return type changes from List<DescribedGroup> to StreamsGroupDescribeResult; (2) a new validateStreamsGroupMember method is added.
Updated method:
  StreamsGroupDescribeResult streamsGroupDescribe(List<String> groupIds, long committedOffset)
New method:
  StreamsGroupMember validateStreamsGroupMember(String groupId, String memberId, long committedOffset)

Type: Class
Name: GroupCoordinatorShard
Location: group-coordinator/src/main/java/org/apache/kafka/coordinator/group/GroupCoordinatorShard.java
Description: Existing shard class. Two changes are required: (1) streamsGroupDescribe return type changes to StreamsGroupDescribeResult; (2) a new void validateStreamsGroupMember method is added that delegates to GroupMetadataManager and propagates exceptions.
Updated method:
  StreamsGroupDescribeResult streamsGroupDescribe(List<String> groupIds, long committedOffset)
New method:
  void validateStreamsGroupMember(String groupId, String memberId, long committedOffset)


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.