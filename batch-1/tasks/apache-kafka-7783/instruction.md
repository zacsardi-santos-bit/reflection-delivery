Implement enhancements to Kafka Connect workers to handle broker coordinator failures more gracefully in distributed mode. Ensure that workers detect coordinator unavailability within a bounded timeout, automatically rejoin the group when the broker is back online, and allow failed tasks to be restarted via the REST interface. Update the rebalance assignor to include group membership identity in task assignments.

*   Update the `WorkerCoordinator` class:
    *   Implement the method `public int generationId()` in `connect/runtime/src/main/java/org/apache/kafka/connect/runtime/distributed/WorkerCoordinator.java`.
        *   Return the current group generation ID as an integer.
        *   Return -1 if no generation is currently defined.

*   Modify the `IncrementalCooperativeAssignor`:
    *   Ensure it calls `coordinator.generationId()` and `coordinator.memberId()` once per task assignment invocation.
    *   Include these calls in every rebalance to correlate assignments with the correct rebalance generation.

*   Enhance Kafka Connect worker behavior:
    *   Detect broker coordinator unavailability within a bounded timeout.
    *   Revoke current assignments if the coordinator remains unreachable after the timeout.
    *   Automatically rejoin the group and resume connector tasks when the broker returns on the same ports, after the rebalance delay.

*   Enable task restartability via REST interface:
    *   Allow failed connector tasks to be restarted through an HTTP POST to `connectors/{name}/tasks/{taskId}/restart`.
    *   Ensure tasks reach the RUNNING state after valid reconfiguration and restart request.

*   Update `EmbeddedKafkaCluster` test utility:
    *   Implement `public void stopOnlyKafka()` in `connect/runtime/src/test/java/org/apache/kafka/connect/util/clusters/EmbeddedKafkaCluster.java`.
        *   Stop Kafka brokers without deleting log directories or stopping ZooKeeper.
    *   Implement `public void startOnlyKafkaOnSamePorts() throws IOException` in the same location.
        *   Restart Kafka brokers on the same ports, reusing log directories.
        *   Maintain `currentBrokerPorts` and `currentBrokerLogDirs` across stop/start cycles.

*   Enhance `EmbeddedConnectCluster` test utility:
    *   Implement `public int executePost(String url, String body, Map<String, String> headers) throws IOException` in `connect/runtime/src/test/java/org/apache/kafka/connect/util/clusters/EmbeddedConnectCluster.java`.
        *   Send an HTTP POST request with `Content-Type` set to "application/json".
        *   Return the HTTP response code as an integer.

*   Ensure Connect worker configuration supports setting `CONNECTOR_CLIENT_POLICY_CLASS_CONFIG` to 'All', allowing connectors to override producer and consumer client settings.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.