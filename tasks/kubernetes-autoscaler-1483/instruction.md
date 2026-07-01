Implement the functionality to enhance the cluster autoscaler for GCE by providing detailed status information for each node in a managed instance group. Update the relevant interfaces and data structures to include lifecycle states and error details for nodes, particularly focusing on creation errors.

*   Update the `GetMigNodes` method in the `GceManager` interface:
    *   Change the return type from `[]string` to `[]cloudprovider.Instance`.
    *   Ensure each `Instance` includes an `Id` and a non-nil `Status` field.

*   Define the `InstanceStatus` struct in `cloudprovider/cloud_provider.go`:
    *   Include fields: `State` of type `InstanceState` and `ErrorInfo` of type `*InstanceErrorInfo`.

*   Define the `InstanceErrorInfo` struct in `cloudprovider/cloud_provider.go`:
    *   Include fields: `ErrorClass` of type `InstanceErrorClass`, `ErrorCode` as `string`, and `ErrorMessage` as `string`.

*   Implement the `InstanceState` type in `cloudprovider/cloud_provider.go` with constants:
    *   `InstanceRunning`, `InstanceCreating`, and `InstanceDeleting`.

*   Implement the `InstanceErrorClass` type in `cloudprovider/cloud_provider.go` with constants:
    *   `OutOfResourcesErrorClass` and `OtherErrorClass`.

*   Modify the `cloudprovider.Instance` struct to include:
    *   A `Status` field of type `*InstanceStatus`.

*   Map GCE `currentAction` values:
    *   'CREATING', 'RECREATING', 'CREATING_WITHOUT_RETRIES' to `InstanceCreating`.
    *   'DELETING' to `InstanceDeleting`.
    *   All other actions to `InstanceRunning`.

*   Populate `ErrorInfo` only for instances in `InstanceCreating` state:
    *   Set `ErrorInfo` to nil for `InstanceRunning` or `InstanceDeleting`.

*   Handle GCE error codes for `InstanceCreating` state:
    *   Map 'QUOTA_EXCEEDED' and codes containing 'QUOTA' to `OutOfResourcesErrorClass` with `ErrorCodeQuotaExceeded`.
    *   Map 'RESOURCE_POOL_EXHAUSTED', 'ZONE_RESOURCE_POOL_EXHAUSTED', and 'ZONE_RESOURCE_POOL_EXHAUSTED_WITH_DETAILS' to `OutOfResourcesErrorClass` with `ErrorCodeStockout`.
    *   Map other errors to `OtherErrorClass` with an empty `ErrorCode`.

*   Prioritize `OutOfResourcesErrorClass` over `OtherErrorClass` when multiple errors are present:
    *   Use the first `OutOfResourcesErrorClass` error to determine `ErrorCode`.

*   Combine all GCE error messages for a creating instance into a single `ErrorMessage`:
    *   Use '; ' as the separator.

*   Declare constants in `gce/autoscaling_gce_client.go`:
    *   `ErrorCodeQuotaExceeded` with value 'QUOTA_EXCEEDED'.
    *   `ErrorCodeStockout` with value 'STOCKOUT'.

*   Update the `FetchMigInstances` method in the `AutoscalingGceClient` interface:
    *   Change the return type from `[]GceRef` to `[]cloudprovider.Instance`.
    *   Incorporate state and error mapping logic.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.