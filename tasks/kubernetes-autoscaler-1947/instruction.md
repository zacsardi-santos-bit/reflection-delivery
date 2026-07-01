Enhance the cluster autoscaler to provide detailed, structured results for node deletion and pod eviction processes. Implement structured reporting for each stage of node deletion, including detailed per-pod eviction outcomes, to facilitate better debugging and monitoring.

*   Update the `drainNode` function in `cluster-autoscaler/core/scale_down.go`:
    *   Add a new parameter `podEvictionHeadroom` of type `time.Duration` as the last argument.
    *   Return a map of `status.PodEvictionResult` keyed by pod name and an error.
    *   Pre-populate the eviction results map with each pod entry marked as `TimedOut=true` and `Err=nil`.
    *   On pod eviction failure, set `TimedOut=true` and `Err` to the eviction error for failed pods; set `TimedOut=false` and `Err=nil` for successful pods.
    *   Handle pod disappearance checks:
        *   If a GET request returns a non-not-found error, set `TimedOut=true` and `Err` to that error.
        *   If GET returns nil (pod still present), set `TimedOut=true` and `Err=nil`.
        *   If GET returns a not-found error, set `TimedOut=false` and `Err=nil`.

*   Define `PodEvictionResult` struct in `cluster-autoscaler/processors/status/scale_down_status_processor.go`:
    *   Fields: `Pod` (*apiv1.Pod), `TimedOut` (bool), `Err` (error).
    *   Implement `WasEvictionSuccessful()` method returning true if `Err` is nil and `TimedOut` is false.

*   Modify the `deleteNode` method in `cluster-autoscaler/core/scale_down.go`:
    *   Return a `NodeDeleteResult` struct instead of a plain error.
    *   Define `NodeDeleteResult` with fields: `Err` (error), `ResultType` (NodeDeleteResultType), `PodEvictionResults` (map[string]PodEvictionResult).
    *   Set `ResultType` to appropriate constants based on the deletion outcome:
        *   `NodeDeleteOk` for successful deletion.
        *   `NodeDeleteErrorFailedToMarkToBeDeleted` if marking the node fails.
        *   `NodeDeleteErrorFailedToEvictPods` if pod eviction fails.
        *   `NodeDeleteErrorFailedToDelete` if cloud provider deletion fails.

*   Define `NodeDeleteResultType` as an integer type in `cluster-autoscaler/processors/status/scale_down_status_processor.go`:
    *   Constants: `NodeDeleteOk`, `NodeDeleteErrorFailedToMarkToBeDeleted`, `NodeDeleteErrorFailedToEvictPods`, `NodeDeleteErrorFailedToDelete`.

*   Ensure `ScaleDownStatus` struct's `NodeDeleteResults` field is of type `map[string]NodeDeleteResult`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.