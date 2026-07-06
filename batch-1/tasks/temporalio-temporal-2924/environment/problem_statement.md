## Description

In a multi-cluster Temporal deployment, namespace changes are not being fully replicated across clusters in two important scenarios:

1. **Namespace registration is not replicated**: When a new global namespace is registered on the active cluster, no replication event is published. As a result, standby clusters are never notified of the new namespace and must be configured manually.

2. **Cluster membership list changes are silently dropped**: When a namespace's cluster membership is updated (e.g., adding or removing clusters), standby clusters that receive the replication task sometimes ignore the update entirely. This happens because the receiving side checks whether the new cluster list includes the local cluster before deciding to apply the change — if the local cluster was removed from the list, the update is skipped and the local copy of the namespace is left with a stale cluster list.

## Expected Behavior

- Registering a global namespace should publish a replication event so standby clusters are notified.
- When the cluster membership list for a namespace is modified, that change should be propagated and applied on all clusters that have a local copy of the namespace, regardless of whether the local cluster appears in the updated list.
- The replication handler should provide a way for callers to indicate when a cluster list change has occurred, so that the replication event is published even in cases that would otherwise be skipped (e.g., when the namespace has only a single cluster in its config).

## Why This Matters

Namespace changes that are not replicated correctly cause divergence between clusters, making global namespaces unavailable or misconfigured on standby clusters. In production multi-cluster setups, this can prevent failover from working correctly and require manual intervention to fix namespace state.
