Implement permission checks in the ArgoCD project update logic to ensure users have the necessary permissions when modifying clusters and repositories. Additionally, create utility functions to assist with these operations.

*   Implement the `DestinationClusters` method in `pkg/apis/application/v1alpha1/types.go`:
    *   Return a `[]string` containing the `Server` field from each `ApplicationDestination` in the `Destinations` slice.
    *   Ensure it returns a non-nil empty `[]string` when the `Destinations` list is empty.

*   Implement the `unique` function in `server/project/util.go`:
    *   Return elements that appear exactly once in the input slice.
    *   Exclude elements appearing two or more times from the result.
    *   Return a non-nil empty `[]string` for empty input or when all elements are duplicated.

*   Update the project update logic to enforce permission checks:
    *   When cluster destinations are removed, verify the caller has 'clusters, update' permission for each removed cluster server URL.
        *   Return a gRPC `PermissionDenied` error with the message 'permission denied: clusters, update, <server_url>' if the caller lacks permission.
    *   When source repositories are removed, verify the caller has 'repositories, update' permission for each removed repository URL.
        *   Return a gRPC `PermissionDenied` error with the message 'permission denied: repositories, update, <repo_url>' if the caller lacks permission.
    *   When the `ClusterResourceWhitelist` changes, ensure the caller has 'clusters, update' permission for each cluster in the updated project's destination list.
        *   Return a gRPC `PermissionDenied` error with the message 'permission denied: clusters, update, <server_url>' if the caller lacks permission.
    *   When the `NamespaceResourceBlacklist` changes, ensure the caller has 'clusters, update' permission for each cluster in the updated project's destination list.
        *   Return a gRPC `PermissionDenied` error with the message 'permission denied: clusters, update, <server_url>' if the caller lacks permission.
    *   Perform these permission checks before any existing destination-in-use validation to ensure permission errors are prioritized.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.