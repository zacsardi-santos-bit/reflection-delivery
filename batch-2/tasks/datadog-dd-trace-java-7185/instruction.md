Implement a class `OtelEnvironmentConfigSource` to map OpenTelemetry system properties and environment variables to their Datadog equivalents when the OpenTelemetry compatibility mode is enabled. Ensure that no OpenTelemetry settings affect Datadog configuration unless explicitly opted in.

*   Implement `OtelEnvironmentConfigSource` class in `internal-api/src/main/java/datadog/trace/bootstrap/config/provider/OtelEnvironmentConfigSource.java`.
    *   Extend `ConfigProvider.Source` and provide a no-argument constructor and a constructor accepting a `Properties` object.
    *   Override `get(String key)` method to return configuration values or `null` based on the conditions.

*   Ensure the following behaviors:
    *   Return `null` for all Datadog configuration keys if the OpenTelemetry compatibility flag is not set to `true`.
    *   Check both `dd.trace.otel.enabled` system property and `DD_TRACE_OTEL_ENABLED` environment variable to determine if translation is active.
    *   Map OpenTelemetry settings to Datadog configuration keys when translation is active:
        *   `otel.service.name` or `OTEL_SERVICE_NAME` to `SERVICE_NAME`.
        *   `OTEL_LOG_LEVEL` to `LOG_LEVEL`.
        *   `otel.propagators` or `OTEL_PROPAGATORS` to `TRACE_PROPAGATION_STYLE`, translating `b3` to `b3single`.
        *   `otel.traces.sampler` or `OTEL_TRACES_SAMPLER` with value `parentbased_traceidratio` to `TRACE_SAMPLE_RATE` using `otel.traces.sampler.arg` or `OTEL_TRACES_SAMPLER_ARG`.
        *   `otel.traces.exporter` or `OTEL_TRACES_EXPORTER` with value `none` to `TRACE_ENABLED` as `false`.
        *   `otel.metrics.exporter` or `OTEL_METRICS_EXPORTER` with value `none` to `RUNTIME_METRICS_ENABLED` as `false`.
        *   Merge HTTP capture headers from client and server properties into `REQUEST_HEADER_TAGS` and `RESPONSE_HEADER_TAGS`.
        *   `otel.javaagent.extensions` or `OTEL_JAVAAGENT_EXTENSIONS` to `TRACE_EXTENSIONS_PATH`.
        *   Parse `otel.resource.attributes` or `OTEL_RESOURCE_ATTRIBUTES` for `SERVICE_NAME`, `ENV`, `VERSION`, and custom attributes to `TAGS`, limited to 10 custom attributes.
    *   Prioritize dedicated service name property over `service.name` in resource attributes for `SERVICE_NAME`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.