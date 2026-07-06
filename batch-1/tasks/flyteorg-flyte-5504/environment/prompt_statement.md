I'm working on adding OTLP trace export support to the OpenTelemetry utilities in the Flyte standard library. Currently, the telemetry configuration only supports exporting traces to Jaeger or a local file, but I need to add support for exporting over OTLP — both via gRPC and HTTP transports — so we can send traces to any modern observability backend.

I also need to add a configurable sampling section to the telemetry config. Right now sampling isn't configurable at all, but operators need to be able to pick a strategy — for instance, always sampling all traces, or using a ratio-based approach.

On top of that, the function that registers tracer providers should be updated to accept a context parameter rather than ignoring context entirely. This is needed so that OTLP exporters (which need a context to establish connections) can be properly initialized.

All of these new configuration options — the OTLP gRPC endpoint, the OTLP HTTP endpoint, and the sampler type — should be exposed as command-line flags so they can be set without modifying config files.
