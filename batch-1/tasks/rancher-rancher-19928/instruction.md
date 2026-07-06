Implement logic to preserve cloud provider password and secret fields during Kubernetes cluster updates. Ensure that when a user omits these fields in an update request, the system retrieves and retains the existing values from the stored configuration, unless new values are explicitly provided.

*   Implement the function `setCloudProviderPasswordFieldsIfNotExists(oldData, newData map[string]interface{})` in `pkg/api/store/cluster/cluster_store.go`.
*   For Azure cloud provider updates:
    *   If `aadClientSecret` is omitted, populate it from the existing configuration.
    *   If `aadClientCertPassword` is omitted, populate it from the existing configuration.
    *   Preserve non-empty values provided for `aadClientSecret` and `aadClientCertPassword`.
*   For vSphere cloud provider updates:
    *   If the global password is omitted, populate it from the existing configuration.
    *   Preserve a non-empty global password if provided.
    *   For each named `virtualCenter` entry:
        *   If the password is omitted and the entry exists in the old configuration, populate it from the old configuration.
        *   Preserve a non-empty password if provided for any `virtualCenter`.
        *   Leave new `virtualCenter` entries with the password as provided.
        *   Ensure removed `virtualCenter` entries do not appear in the new configuration.
*   For OpenStack cloud provider updates:
    *   If the global password is omitted, populate it from the existing configuration.
    *   Preserve a non-empty global password if provided.
*   Ensure non-password fields remain unchanged during the password back-fill process.
*   If no vSphere configuration is present in the new data, return without modifying it.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.