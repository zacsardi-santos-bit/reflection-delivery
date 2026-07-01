Implement security hardening for the Consul Kubernetes service mesh webhook by modifying the security context of injected containers. Ensure that both the sidecar proxy and init containers explicitly prevent privilege escalation and apply a read-only root filesystem where required.

*   Update the Consul dataplane sidecar container's security context:
    *   Set `AllowPrivilegeEscalation` to `false` using `pointer.Bool(false)`.
    *   Ensure this setting is applied in both the standard and v2 webhook paths.
    *   Modify the following files:
        *   `control-plane/connect-inject/webhook/consul_dataplane_sidecar.go`
        *   `control-plane/connect-inject/webhookv2/consul_dataplane_sidecar.go`

*   Update the init container's security context when CNI is enabled and transparent proxy is not separately enabled:
    *   Set `ReadOnlyRootFilesystem` to `true` using `pointer.Bool(true)`.
    *   Set `AllowPrivilegeEscalation` to `false` using `pointer.Bool(false)`.
    *   Ensure these settings are applied in both the standard and v2 webhook paths.
    *   Modify the following files:
        *   `control-plane/connect-inject/webhook/container_init.go`
        *   `control-plane/connect-inject/webhookv2/container_init.go`

*   Ensure all changes are consistent across the webhook package and the webhookv2 package.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.