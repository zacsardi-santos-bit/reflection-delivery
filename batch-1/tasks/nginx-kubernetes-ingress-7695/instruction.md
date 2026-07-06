Implement a telemetry feature for the NGINX Ingress Controller that reports recognized configuration keys from two ConfigMaps: the main configuration map and the management configuration map. Ensure these keys are included in the telemetry data payload, filtering out unrecognized keys.

*   Implement the `ConfigMapKeys` method in `internal/telemetry/cluster.go`:
    *   Accepts a `context.Context` and returns a `([]string, error)`.
    *   Reads the ConfigMap identified by `CollectorConfig.MainConfigMapName`.
    *   Returns keys present in a known allowlist of recognized NIC configuration keys.
    *   Returns `nil` if no recognized keys are present.

*   Implement the `MGMTConfigMapKeys` method in `internal/telemetry/cluster.go`:
    *   Accepts a `context.Context` and returns a `([]string, error)`.
    *   Reads the ConfigMap identified by `CollectorConfig.MGMTConfigMapName`.
    *   Returns keys present in a known allowlist of recognized MGMT configuration keys.
    *   Returns `nil` if no recognized keys are present.

*   Update the `NICResourceCounts` struct in `internal/telemetry/exporter.go`:
    *   Add `ConfigMapKeys []string` to hold filtered keys from the main ConfigMap.
    *   Add `MGMTConfigMapKeys []string` to hold filtered keys from the MGMT ConfigMap.
    *   Ensure both fields are JSON-serializable with field names "ConfigMapKeys" and "MGMTConfigMapKeys" respectively.

*   Update the `CollectorConfig` struct in `internal/telemetry/collector.go`:
    *   Add `MainConfigMapName string` to specify the main NIC ConfigMap in "namespace/name" format.
    *   Add `MGMTConfigMapName string` to specify the MGMT ConfigMap in "namespace/name" format.

*   Ensure the `Collect` method:
    *   Populates `NICResourceCounts.ConfigMapKeys` with filtered keys from the main ConfigMap.
    *   Populates `NICResourceCounts.MGMTConfigMapKeys` with filtered keys from the MGMT ConfigMap.
    *   Includes both fields in the JSON-serialized telemetry output.

*   Define key allowlists:
    *   Main ConfigMap allowlist includes keys like 'proxy-buffering', 'zone-sync', 'error-log-level'.
    *   Exclude keys such as 'enforce-initial-report', 'sync', and unknown keys.
    *   MGMT ConfigMap allowlist includes keys like 'enforce-initial-report', 'license-token-secret-name'.
    *   Exclude keys such as 'zone-sync', 'license', and unknown keys.

*   Implement a `JSONExporter` struct in `internal/telemetry/exporter.go`:
    *   Include an `Endpoint io.Writer` field.
    *   Marshal telemetry data to JSON and write it to the `Endpoint`.
    *   Ensure it implements the telemetry exporter interface.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.