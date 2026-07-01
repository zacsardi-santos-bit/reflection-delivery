Implement the function `RenderPrometheusConfig` to generate Prometheus configuration for monitoring three core database components in a Kubernetes cluster. Ensure the configuration handles DNS-based address resolution and correctly applies TLS settings based on the cluster's TLS configuration.

*   Implement `RenderPrometheusConfig(cmodel *MonitorConfigModel) (string, error)` in `pkg/monitor/monitor/template.go`.
    *   Ensure it returns a YAML string and a nil error for valid inputs.
    *   Return a non-nil error only if template rendering fails.

*   Address Relabeling:
    *   Use source labels `[__meta_kubernetes_pod_name, __meta_kubernetes_pod_label_app_kubernetes_io_instance, __meta_kubernetes_pod_annotation_prometheus_io_port]` with regex `(.+);(.+);(.+)` targeting `__address__`.
    *   For `pd` scrape job, use replacement `'$1.$2-pd-peer:$3'`.
    *   For `tidb` scrape job, use replacement `'$1.$2-tidb-peer:$3'`.
    *   For `tikv` scrape job, use replacement `'$1.$2-tikv-peer:$3'`.

*   Scheme Configuration:
    *   Always include an explicit `scheme` field in scrape job configs for `pd`, `tidb`, and `tikv`.
    *   When `EnableTLSCluster` is false or not set:
        *   Use `scheme: http` for all three components.
    *   When `EnableTLSCluster` is true:
        *   Use `scheme: https` for `pd` and `tidb` with `tls_config`:
            *   `ca_file: /var/lib/cluster-client-tls/ca.crt`
            *   `cert_file: /var/lib/cluster-client-tls/tls.crt`
            *   `key_file: /var/lib/cluster-client-tls/tls.key`
            *   `insecure_skip_verify: false`
        *   Use `scheme: http` for `tikv` with `tls_config: {insecure_skip_verify: true}`.

*   Ensure the `MonitorConfigModel` struct includes:
    *   `ReleaseTargetRegex *config.Regexp`
    *   `ReleaseNamespaces []string`
    *   `EnableTLSCluster bool`

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.