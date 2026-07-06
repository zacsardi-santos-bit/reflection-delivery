Implement automatic generation of Kubernetes RBAC resources for the OpenTelemetry Operator based on the collector's configuration. Ensure that the necessary cluster roles and bindings are created when resource detection processors require access to Kubernetes or OpenShift APIs, improving user experience by reducing manual configuration.

*   Implement the `ConfigToRBAC` function in `internal/manifests/collector/adapters/config_to_rbac.go`.
    *   Accept a `logger` and a parsed collector configuration `map`.
    *   Return a slice of `[]rbacv1.PolicyRule`.
    *   Return a typed nil (not an empty slice) when no rules are required.
    *   Return nil if the config has no processors section or if processors do not require Kubernetes API access.
    *   Return a `PolicyRule` with `APIGroups=['']`, `Resources=['nodes']`, `Verbs=['get','list']` for a 'kubernetes' detector.
    *   Return a `PolicyRule` with `APIGroups=['config.openshift.io']`, `Resources=['infrastructures','infrastructures/status']`, `Verbs=['get','watch','list']` for an 'openshift' detector.

*   Implement the `ClusterRole` function in `internal/manifests/collector/rbac.go`.
    *   Accept a `Params` value.
    *   Return nil if no RBAC rules are required.
    *   Return a non-nil `*rbacv1.ClusterRole` with `Rules` derived from `ConfigToRBAC` if RBAC is required.

*   Implement the `ClusterRoleBinding` function in `internal/manifests/collector/rbac.go`.
    *   Accept a `Params` value.
    *   Return nil if no RBAC rules are required.
    *   Return a non-nil `*rbacv1.ClusterRoleBinding` if RBAC rules are required.

*   Provide test data YAML files:
    *   `internal/manifests/collector/testdata/rbac_resourcedetectionprocessor_k8s.yaml` for a resourcedetection processor with a kubernetes detector.
    *   `internal/manifests/collector/testdata/rbac_resourcedetectionprocessor_openshift.yaml` for a resourcedetection processor with an openshift detector.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.