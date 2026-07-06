I need to add distributed tracing support to my ingress controller. Right now there's no way to enable OpenTracing-compatible tracing for traffic passing through the controller. I want operators to be able to pick a tracing backend — like Jaeger, Zipkin, or Datadog — by specifying a collector host in the global configuration, and have the controller automatically load the right tracing plugin and emit the correct configuration directives.

The tracing configuration also needs to work at the per-route level, so individual Ingress resources should be able to enable or disable tracing via Kubernetes annotations, independent of the global setting. The controller should respect whether the route uses a standard HTTP backend or a gRPC backend and emit the appropriate context-propagation directive for each.

Additionally, I'd like support for controlling whether incoming trace spans from external clients are trusted, both globally and per route. And there should be a way to configure custom operation name templates for trace spans at both the global and per-location levels.

When tracing is fully disabled (globally and not overridden in any location), no tracing directives should appear in the generated nginx configuration at all.
