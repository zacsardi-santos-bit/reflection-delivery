## Description

The ingress controller currently has no support for distributed tracing via OpenTracing-compatible backends. Operators who want to instrument their services with distributed tracing have no way to configure the controller to load a tracing plugin or to emit trace spans for incoming requests.

## Expected Behavior

- Operators should be able to globally enable distributed tracing and select a backend (Jaeger, Zipkin, or Datadog) by providing the collector host in the controller's configuration.
- When a collector host or endpoint is configured, the controller should automatically load the appropriate tracing plugin shared library.
- Operators should be able to configure whether incoming trace context headers from upstream clients are trusted.
- When tracing is active, the controller should inject the appropriate context propagation directive into each location block, choosing between a standard HTTP propagation directive and a gRPC-specific one depending on the backend protocol.
- Operators should be able to override the global tracing enable/disable setting on a per-route (per-Ingress) basis using Kubernetes annotations.
- Operators should be able to configure a custom operation name template for trace spans at both the global level and the per-location level.
- When tracing is disabled globally and not enabled for a specific location, no tracing directives should appear in the generated configuration.

## Why This Matters

Without this feature, operators cannot integrate their ingress-managed services with distributed tracing infrastructure, making it difficult to observe request flows across microservices. This capability is essential for production observability.
