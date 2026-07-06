## Description

The OpenTelemetry utilities library in Flyte currently supports exporting distributed traces to Jaeger and local file outputs, but has no support for the OTLP protocol (OpenTelemetry Protocol). OTLP is the standard protocol used by modern observability platforms and collectors such as OpenTelemetry Collector. Without it, teams cannot route Flyte traces to widely-used backends like Grafana Tempo, Datadog, or any OTLP-compatible collector over gRPC or HTTP.

Additionally, trace sampling is currently hardcoded with no operator control. There is no way to configure whether all traces should be sampled, or whether a ratio-based sampling strategy should be applied.

## Expected Behavior

- Two new exporter options should be available in the telemetry configuration: one for sending traces to an OTLP gRPC endpoint and one for an OTLP HTTP endpoint. Each should have a configurable endpoint URL.
- A new sampler configuration section should allow operators to choose the sampling strategy (e.g., always sample all traces, or use a trace ID ratio-based sampler).
- The function used to register tracer providers should accept a context parameter to enable proper lifecycle and context-aware initialization.
- All new configuration fields should be settable via command-line flags.

## Why This Matters

Without OTLP support, Flyte operators are limited to Jaeger or local file exports, which prevents integration with the modern observability ecosystem. Adding OTLP exporters and configurable sampling makes Flyte's tracing compatible with industry-standard backends and gives platform teams control over trace volume and routing.
