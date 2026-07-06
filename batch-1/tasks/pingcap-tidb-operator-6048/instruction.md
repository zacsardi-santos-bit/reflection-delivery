Update the TiDB Kubernetes operator to relocate the bootstrap SQL configuration from the TiDB component's security settings to the cluster specification level. Ensure that all relevant logic and tests reflect this change.

*   Modify the `ClusterSpec` struct in `apis/core/v1alpha1/cluster_types.go`:
    *   Add a new field `BootstrapSQL` of type `*corev1.LocalObjectReference`.
    *   Ensure this field references a ConfigMap containing SQL statements for cluster initialization.
    
*   Update the `TiDBSecurity` struct in `apis/core/v1alpha1/tidb_types.go`:
    *   Remove the `BootstrapSQL` field as it is no longer relevant at this level.

*   Adjust the TiDB configuration logic:
    *   In `pkg/configs/tidb/config.go`, modify the overlay logic to check `cluster.Spec.BootstrapSQL != nil` to determine if bootstrap SQL is enabled.

*   Update generated deep copy code for v1alpha1 types:
    *   Reflect the removal of `BootstrapSQL` from `TiDBSecurity`.
    *   Reflect the addition of `BootstrapSQL` to `ClusterSpec`.

*   Ensure the `TestOverlay` test in `pkg/configs/tidb`:
    *   Compiles and passes with `BootstrapSQL` set at the `ClusterSpec` level, not the `TiDB` security level.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.