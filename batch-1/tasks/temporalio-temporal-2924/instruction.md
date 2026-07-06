Implement the necessary changes to ensure namespace registration and cluster membership updates are correctly replicated across clusters in a multi-cluster Temporal setup. Update the relevant interfaces and methods to handle these replication tasks effectively.

*   Update the `NamespaceReplicator` interface in `common/namespace/transmissionTaskHandler.go`:
    *   Add a new boolean parameter `replicationClusterListUpdated` between `replicationConfig` and `configVersion` in the `HandleTransmissionTask` method signature.

*   Modify the `HandleTransmissionTask` method in `common/namespace/transmissionTaskHandler.go`:
    *   Ensure that when `isGlobalNamespace` is false, no replication task is published, regardless of `replicationClusterListUpdated`.
    *   Ensure that when `isGlobalNamespace` is true and `replicationClusterListUpdated` is false, no replication task is published if the replication config contains only one cluster.
    *   Ensure that when `isGlobalNamespace` is true and `replicationClusterListUpdated` is true, a replication task is published even if the replication config contains only one cluster.

*   Update the `RegisterNamespace` function in `common/namespace/handler.go`:
    *   Call `HandleTransmissionTask` with `replicationClusterListUpdated` set to true to ensure a replication task is published when registering a global namespace.

*   Modify the `UpdateNamespace` function:
    *   Ensure it calls `HandleTransmissionTask` with `replicationClusterListUpdated` set to true when the cluster list of a global namespace is updated.
    *   Set `replicationClusterListUpdated` to false when the cluster list is not updated.

*   Update the `Execute` function in `common/namespace/replicationTaskExecutor.go`:
    *   Ensure it correctly applies namespace UPDATE replication tasks that change the active cluster and cluster list.
    *   After processing, update the database to reflect the new active cluster name, new cluster list, incremented config version, failover version, failover notification version, and notification version.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.