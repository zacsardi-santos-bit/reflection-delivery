Implement a configurable option in Kueue's wait-for-pods-ready feature to allow operators to choose the timestamp used for re-queuing workloads after eviction. Provide two strategies: using the eviction timestamp (default) or the original creation timestamp. Ensure that the system maintains backward compatibility and respects the chosen strategy in both blocking and non-blocking admission modes.

*   Define a new type alias `RequeuingTimestamp` in `apis/config/v1beta1/configuration_types.go` with constants:
    *   `EvictionTimestamp`: "Eviction"
    *   `CreationTimestamp`: "Creation"

*   Update the `WaitForPodsReady` struct in `apis/config/v1beta1/configuration_types.go`:
    *   Add an optional pointer field `RequeuingTimestamp` of type `*RequeuingTimestamp` with JSON tag `json:"requeuingTimestamp,omitempty"`.

*   Modify `SetDefaults_Configuration` to:
    *   Default `RequeuingTimestamp` to `EvictionTimestamp` if nil.
    *   Preserve existing `RequeuingTimestamp` values if set.

*   Introduce a package-level unexported constant `defaultJobFrameworkName` with value "batch/job" in `apis/config/v1beta1/defaults.go`.

*   Create an `Ordering` struct in `pkg/workload/workload.go`:
    *   Include a field `PodsReadyRequeuingTimestamp` of type `config.RequeuingTimestamp`.
    *   Implement `GetQueueOrderTimestamp(w *kueue.Workload) *metav1.Time`:
        *   Return `LastTransitionTime` of the `WorkloadEvicted` condition if `EvictionTimestamp` is used and the condition is met.
        *   Return `CreationTimestamp` if `CreationTimestamp` is used or no condition is met.

*   Develop a `queueOrderingFunc` in `pkg/queue/cluster_queue_strict_fifo.go`:
    *   Accept a `workload.Ordering` and return a comparison function sorting workloads by priority and `GetQueueOrderTimestamp`.

*   Update cluster queue creation functions:
    *   `newClusterQueue`, `newClusterQueueStrictFIFO`, and `newClusterQueueBestEffortFIFO` to accept `workload.Ordering` and use `queueOrderingFunc`.

*   Enhance `queue.NewManager` and `scheduler.New` to accept variadic `Option` arguments:
    *   Provide `WithPodsReadyRequeuingTimestamp` option functions in `pkg/queue/manager.go` and `pkg/scheduler/scheduler.go`.

*   Modify `preemption.New` in `pkg/scheduler/preemption/preemption.go`:
    *   Accept `workload.Ordering` as the second argument and use it for preemption candidate selection.

*   Redefine `entryOrdering` in `pkg/scheduler/scheduler.go`:
    *   As a struct with `entries` and `workloadOrdering`.
    *   Implement `Less` using `workloadOrdering.GetQueueOrderTimestamp`.

*   Ensure that when configured with `CreationTimestamp`, evicted workloads are re-admitted before later-created workloads, consistent across admission modes.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.