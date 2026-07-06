Implement a unified endpoint release operation in the Spiderpool garbage collection system to handle all lifecycle scenarios of network endpoints safely. Ensure that the operation is part of the workload endpoint manager's interface, allowing other components to utilize it effectively.

*   Update the WorkloadEndpointManager interface:
    *   Add a new method `ReleaseEndpointAndFinalizer` with the signature: `ReleaseEndpointAndFinalizer(ctx context.Context, namespace string, podName string, cached bool) error`.
    *   Implement this method in the `workloadEndpointManager` struct located in `pkg/workloadendpointmanager/workloadendpoint_manager.go`.

*   Implement the `ReleaseEndpointAndFinalizer` method to handle endpoint lifecycle scenarios:
    *   If the endpoint does not exist (returns a not-found error), return `nil` without an error.
    *   If retrieving the endpoint results in a non-404 error, return that error.
    *   If the endpoint exists and its `DeletionTimestamp` is `nil`:
        *   Call `DeleteEndpoint`.
        *   If `DeleteEndpoint` fails, return the error.
    *   If the endpoint exists with a finalizer and `DeletionTimestamp` is `nil`:
        *   Remove the finalizer using a client `Update` call.
        *   Return `nil` on success.
    *   If the endpoint has a `DeletionTimestamp` set:
        *   Call `RemoveFinalizer`.
        *   Return `nil` on success.
    *   If `RemoveFinalizer` returns an error, return that error.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.