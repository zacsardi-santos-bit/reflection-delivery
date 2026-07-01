## Description

When translating OpenTelemetry metrics to the Prometheus remote-write format, the system currently does not distinguish between cumulative and delta aggregation temporality for histograms. This means delta histograms are not properly marked in the output — they should be treated as gauge-type (no-reset) metrics rather than counter-type metrics, but right now both receive the same treatment.

Beyond the incorrect output representation, there is also no mechanism to configure whether delta-temporality metrics should be accepted at all. Operators deploying Prometheus as an OTLP ingestion target may want to reject delta metrics entirely (since Prometheus is inherently a cumulative system), but there is currently no setting to enforce this.

## Expected Behavior

- Cumulative histograms should be marked as counter-type resets in the output; delta histograms should be marked as gauge-type (no-reset) metrics.
- A configuration option should control whether delta-temporality metrics are accepted. When delta is not allowed, metrics with delta temporality should be rejected with a clear error.
- Metrics with unspecified temporality should always be rejected as invalid, even when delta is configured as allowed.
- When some metrics in a batch are rejected due to temporality, the valid metrics should still be converted and returned alongside the error.
- Metric types that do not carry temporality (such as summaries and gauges) should be unaffected by any temporality setting.
- Metrics with unsupported types should produce a descriptive error message indicating the unsupported type.
- The API server should expose a new configuration option for enabling native delta histogram ingestion.

## Why This Matters

Without proper temporality handling, delta histograms are silently misrepresented in Prometheus — they appear to reset like counters when they should not. This leads to incorrect query results and can confuse users who rely on these metrics for monitoring. Giving operators the ability to reject delta metrics also helps enforce data quality guarantees in environments where only cumulative metrics are expected.
