## Description

Jaeger's ClickHouse storage backend is missing the ability to serve latency metrics. When a user configures Jaeger with ClickHouse as the backend, they currently cannot query service performance metrics (such as latency percentiles) because there is no metrics reader implementation for ClickHouse. The storage backend has span data in ClickHouse but no mechanism to aggregate and expose it as time-series metrics through the standard Jaeger metrics API.

## Expected Behavior

- A metrics reader for ClickHouse should be added that implements the standard Jaeger metrics reader interface
- Querying latency metrics should return results grouped by service, with each service's data points as gauge values over time
- When queried with operation grouping enabled, latency results should be grouped by both service and operation name
- The step size parameter should be handled gracefully: invalid (nil, zero, or negative) values fall back to a default; sub-second values are clamped to one second; fractional seconds are truncated
- Span kind strings in the standard OpenTelemetry format should be converted to the format stored in ClickHouse
- Call rate, error rate, and minimum step duration queries should return a clear "not implemented" error to signal that those features are not yet available

## Why This Matters

Without this metrics reader, ClickHouse-backed Jaeger deployments cannot expose service performance metrics to the frontend or downstream systems. Adding even a partial implementation (with latency support and explicit stubs for the rest) lets users start using SPM (Service Performance Monitoring) features with ClickHouse.
