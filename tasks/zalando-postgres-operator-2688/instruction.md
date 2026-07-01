Implement support for setting ownership relationships between a Postgres custom resource and its child resources in the Postgres operator for Kubernetes. Introduce a configuration toggle to enable or disable this feature, ensuring cascading deletion and improved observability.

*   Update the Resources configuration struct:
    *   Add a boolean pointer field `EnableOwnerReferences` with JSON name 'enable_owner_references', defaulting to false.
    *   Ensure it is the first field in the struct located in `pkg/util/config/config.go`.

*   Modify the `ownerReferences()` method in `pkg/cluster/k8sres.go`:
    *   When `EnableOwnerReferences` is nil or false, return existing owner references unchanged.
    *   When true, append an `OwnerReference` pointing to the Postgresql custom resource with:
        *   Name: `cluster.Postgresql.ObjectMeta.Name`
        *   Kind: "Postgresql"
        *   APIVersion: "acid.zalan.do/v1"
        *   Controller: true
        *   UID: `cluster.Postgresql.ObjectMeta.UID`

*   Update resource generation methods to set owner references:
    *   `generatePodDisruptionBudget()` must set `OwnerReferences` using `ownerReferences()`.
    *   `generateLogicalBackupJob()` must set `OwnerReferences` on the CronJob's `ObjectMeta` to match `ownerReferences()`.
    *   `generateStatefulSet()`, `generateService()`, `generateEndpoint()`, and `generateSingleUserSecret()` must set `OwnerReferences` using `ownerReferences()`.
        *   Exclude cross-namespace secrets from having owner references.

*   Enhance the `compareServices` function in `pkg/cluster/cluster.go`:
    *   Compare `OwnerReferences` between current and new services.
    *   Return `match=false` if owner references differ or are missing in the current service.

*   Ensure the connection pooler deployment logic handles `EnableOwnerReferences` without error.

*   Exclude certain resources from owner references:
    *   Persistent Volume Claims
    *   Patroni configuration service and endpoint
    *   Cross-namespace secrets

*   Implement dynamic updates:
    *   Detect mismatches in ownership metadata and trigger updates during the operator's sync cycle.
    *   Ensure the feature is disabled by default and can be toggled dynamically.

*   Update the `KubernetesMetaConfiguration` struct in `pkg/apis/acid.zalan.do/v1/operator_configuration_type.go`:
    *   Add `EnableOwnerReferences` as the first field with JSON name 'enable_owner_references'.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.