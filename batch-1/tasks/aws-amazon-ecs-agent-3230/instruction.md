Implement configurable URLs for the ECS agent's service connect proxy communication endpoints, replacing hardcoded URLs. Ensure that these URLs are part of the task runtime configuration and accessible after setup. Additionally, update environment variables for service connect containers to disable IAM authentication for XDS and use the correct Unix socket URI format for relay socket paths.

*   Update the `RuntimeConfig` struct in `agent/api/task/service_connect.go`:
    *   Add a `StatsRequest` field with the JSON tag `statsRequest` to hold the full HTTP URL for stats requests.
    *   Add a `DrainRequest` field with the JSON tag `drainRequest` to hold the full HTTP URL for drain requests.

*   Modify methods in `agent/api/appnet/client_linux.go`:
    *   Update `GetStats(config task.RuntimeConfig) (map[string]*prometheus.MetricFamily, error)` to use `config.StatsRequest` as the URL for HTTP GET requests.
    *   Update `DrainInboundConnections(config task.RuntimeConfig) error` to use `config.DrainRequest` as the URL for HTTP GET requests.

*   Update the `manager` struct in `agent/engine/service_connect/manager_linux.go`:
    *   Add an `adminStatsRequest` field to hold the HTTP URL for stats requests.
    *   Add an `adminDrainRequest` field to hold the HTTP URL for drain requests.

*   Ensure the service connect manager populates runtime configuration:
    *   Set `RuntimeConfig.StatsRequest` from `adminStatsRequest` when configuring a task's agent container.
    *   Set `RuntimeConfig.DrainRequest` from `adminDrainRequest` when configuring a task's agent container.

*   Ensure the task's `GetServiceConnectRuntimeConfig()` method returns a `RuntimeConfig` including `StatsRequest` and `DrainRequest`.

*   Configure service connect container environment variables:
    *   Include `ENVOY_ENABLE_IAM_AUTH_FOR_XDS` set to "0".
    *   Prefix the relay socket path with "unix://" (e.g., convert `/some/run/relay_file` to `unix:///some/run/relay_file`).

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.