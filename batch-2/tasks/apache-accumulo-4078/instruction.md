Implement a mechanism to detect and report idle server processes in Accumulo clusters, and enable custom tagging of metrics for better observability. Ensure that server processes emit an idle metric after a configurable idle period and support custom key-value tags on all metrics.

*   Define a constant `METRICS_SERVER_IDLE` with the value `'accumulo.server.idle'` in the `MetricsProducer` interface located at `core/src/main/java/org/apache/accumulo/core/metrics/MetricsProducer.java`.
*   Add configuration properties to the `Property` enum in `core/src/main/java/org/apache/accumulo/core/conf/Property.java`:
    *   `GENERAL_IDLE_PROCESS_INTERVAL` with the key `'general.metrics.process.idle'` to specify the idle duration before emitting the idle metric. It should accept time duration values.
    *   `GENERAL_MICROMETER_USER_TAGS` with the key `'general.micrometer.user.tags'` to accept a comma-separated list of 'key=value' pairs for tagging metrics.
*   Ensure all metrics emitted by server processes include a `process.name` tag:
    *   Use `'tserver'` for tablet servers.
    *   Use `'sserver'` for scan servers.
    *   Use `'compactor'` for compactors.
*   Implement the emission of the `accumulo.server.idle` metric for tablet servers, scan servers, and compactors when they have been idle for the duration specified by `GENERAL_IDLE_PROCESS_INTERVAL`. Tag these metrics with the appropriate `process.name`.
*   Ensure the `accumulo.server.idle` metric is not emitted during normal active operations.
*   When `GENERAL_MICROMETER_USER_TAGS` is configured, append the specified tags to all metrics emitted by server processes.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.