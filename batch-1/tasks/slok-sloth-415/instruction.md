Update the Helm chart's deployment template and default values to ensure that security context sections are only rendered when explicitly configured. Simplify the security context configuration options by removing unnecessary settings.

*   Modify the Helm deployment template (`deploy/kubernetes/helm/sloth/templates/deployment.yaml`):
    *   Ensure no `securityContext` block is rendered at the pod or container level when default values are used.
    *   Render pod-level `securityContext` fields (`fsGroup`, `runAsGroup`, `runAsNonRoot`, `runAsUser`) only when explicitly configured.
    *   Render container-level `securityContext` field (`allowPrivilegeEscalation`) only when explicitly configured.
    *   Ensure the same container-level security context settings apply to all containers in the deployment.

*   Update the default Helm values file (`deploy/kubernetes/helm/sloth/values.yaml`):
    *   Set `securityContext.pod` and `securityContext.container` to `null` by default to prevent rendering of security context sections.
    *   Remove support for `supplementalGroups` and `capabilities` fields.

*   Adjust test configurations and expectations:
    *   Ensure `deploy/kubernetes/helm/sloth/tests/testdata/output/deployment_default.yaml` has no `securityContext` blocks.
    *   Ensure `deploy/kubernetes/helm/sloth/tests/testdata/output/deployment_custom.yaml`, `deployment_custom_no_extras.yaml`, and `deployment_custom_slo_config.yaml` include appropriate pod and container security context settings.
    *   Delete `deploy/kubernetes/helm/sloth/tests/testdata/output/deployment_securityContext.yaml` as its corresponding test is removed.
    *   Update `customValues()` in `deploy/kubernetes/helm/sloth/tests/values_test.go` to include necessary security context values and remove the `securityValues()` function.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.