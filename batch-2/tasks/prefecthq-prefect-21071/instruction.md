I'd like Prefect to automatically collect and export system resource metrics (like CPU and memory usage) while a flow run is executing.

*   The TelemetrySettings model must expose an enable_resource_metrics field that defaults to True and can be overridden via the PREFECT_TELEMETRY_ENABLE_RESOURCE_METRICS environment variable.

*   The TelemetrySettings model must expose a resource_metrics_interval_seconds field that defaults to 10 and can be overridden via the PREFECT_TELEMETRY_RESOURCE_METRICS_INTERVAL_SECONDS environment variable.

*   Both PREFECT_TELEMETRY_ENABLE_RESOURCE_METRICS and PREFECT_TELEMETRY_RESOURCE_METRICS_INTERVAL_SECONDS must be registered as supported Prefect settings.

*   _resolve_metrics_endpoint(settings) must return a two-element tuple of (endpoint_url, is_cloud). When OTEL_EXPORTER_OTLP_METRICS_ENDPOINT is set, it must return that value as endpoint and False as is_cloud, regardless of whether the settings indicate a cloud connection.

*   _resolve_metrics_endpoint must fall back to OTEL_EXPORTER_OTLP_ENDPOINT when the metrics-specific env var is absent, appending '/v1/metrics' to the base URL (stripping any trailing slash first). is_cloud must be False in this case regardless of the cloud connection setting.

*   _resolve_metrics_endpoint must return (settings.api.url.rstrip('/') + '/telemetry/v1/metrics', True) when connected_to_cloud is True and no OTEL endpoint env vars are set. If api.url is None or the settings do not indicate a cloud connection and no OTEL env vars are set, endpoint must be None.

*   RunMetrics must be a context manager accepting (flow_run, flow) positional arguments. When telemetry is disabled (enable_resource_metrics is False), RunMetrics must behave as a no-op without raising any exception.

*   RunMetrics must behave as a no-op when _resolve_metrics_endpoint returns a None endpoint.

*   RunMetrics must behave as a no-op when the required OpenTelemetry libraries (system metrics instrumentor or OTLP exporter) cannot be imported, without raising any exception.

*   When setup succeeds, RunMetrics must call instrument(meter_provider=meter_provider) on the system metrics instrumentor upon entering the context, and must call uninstrument() on the instrumentor and shutdown() on the meter provider upon exiting the context.

*   If any exception occurs during RunMetrics setup (e.g. a malformed endpoint URL), RunMetrics must degrade to a no-op and must not prevent the body of the context from executing.

*   When the resolved endpoint is not a cloud endpoint (is_cloud is False), RunMetrics must create the OTLP metric exporter without passing a 'headers' keyword argument, so that OTEL_EXPORTER_OTLP_HEADERS environment variable configuration is respected.

*   When the resolved endpoint is a cloud endpoint (is_cloud is True), RunMetrics must create the OTLP metric exporter with headers={'Authorization': 'Bearer <api_key>'} where api_key is obtained from settings.api.key.get_secret_value().

*   The prefect.engine module must import RunMetrics using the statement 'from prefect.telemetry.metrics import RunMetrics' and must reference RunMetrics in its source.


*   Interface details: Type: Class
Name: TelemetrySettings
Location: src/prefect/settings/models/telemetry.py
Description: Pydantic settings model for telemetry configuration. Must expose enable_resource_metrics (bool, default True) and resource_metrics_interval_seconds (int, default 10) fields, both controllable via corresponding PREFECT_TELEMETRY_* environment variables.

Type: Function
Name: _resolve_metrics_endpoint
Location: src/prefect/telemetry/metrics.py
Signature: _resolve_metrics_endpoint(settings) -> tuple[str | None, bool]
Description: Resolves the OTLP metrics endpoint and whether it is a Prefect Cloud endpoint. Checks OTEL_EXPORTER_OTLP_METRICS_ENDPOINT first, then OTEL_EXPORTER_OTLP_ENDPOINT (appending /v1/metrics), then derives from settings.api.url when connected_to_cloud is True. Returns (None, False) when no endpoint can be resolved. When an endpoint is overridden via environment variable, is_cloud must always be False even if the settings indicate a cloud connection.

Type: Function (context manager)
Name: RunMetrics
Location: src/prefect/telemetry/metrics.py
Signature: RunMetrics(flow_run, flow) -> context manager (usable with the 'with' statement)
Description: A context manager (may be implemented as a class with __enter__/__exit__ or as a contextmanager-decorated generator function) that instruments system resource metric collection for a flow run using OpenTelemetry. Accepts positional arguments (flow_run, flow). On entry, sets up a SystemMetricsInstrumentor with a MeterProvider backed by an OTLPMetricExporter; calls instrument(meter_provider=meter_provider) on the instrumentor. On exit, calls uninstrument() on the instrumentor and shutdown() on the meter provider. Degrades to a no-op when resource metrics are disabled, when no endpoint is resolved, when required libraries cannot be imported, or when setup raises any exception. For cloud endpoints (is_cloud=True), passes headers={'Authorization': 'Bearer <api_key>'} to the OTLPMetricExporter; for non-cloud endpoints, does not pass a headers keyword argument to the exporter.

Note: The prefect.engine module (src/prefect/engine.py) must contain the exact import statement "from prefect.telemetry.metrics import RunMetrics" and must reference RunMetrics within its source code.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.