Implement distributed tracing support in the ingress controller to enable operators to configure OpenTracing-compatible tracing for traffic. Allow selection of a tracing backend (Jaeger, Zipkin, or Datadog) by specifying a collector host in the global configuration, and ensure the controller loads the correct tracing plugin and emits appropriate configuration directives. Support per-route tracing configuration via Kubernetes annotations.

Requirements:

* Implement the `NewParser` function in `internal/ingress/annotations/opentracing/main.go`:
    * Accept a `resolver.Resolver` and return a parser with a `Parse` method.
    * Ensure `Parse` processes a Kubernetes Ingress object and returns a `*Config` value without error.
* Define the `Config` struct in `internal/ingress/annotations/opentracing/main.go`:
    * Include boolean fields: `Enabled`, `TrustEnabled`, `Set`, and `TrustSet`.
    * Set `Enabled` to true if `enable-opentracing` annotation is 'true', false otherwise.
    * Set `TrustEnabled` to true if `trust-span` annotation is 'true' alongside `enable-opentracing`.
* Implement `opentracingPropagateContext` in `internal/ingress/controller/template/template.go`:
    * Accept an `*ingress.Location` and return the appropriate context-propagation directive string.
    * Return "opentracing_propagate_context;" for HTTP, HTTPS, auto-HTTP, and FCGI protocols.
    * Return "opentracing_grpc_propagate_context;" for gRPC and gRPCS protocols.
    * Return an empty string for a nil location.
* Implement `buildOpentracing` in `internal/ingress/controller/template/template.go`:
    * Accept a configuration and a slice of servers.
    * Return an empty string for invalid input types.
    * Return "\r\n" if `EnableOpentracing` is true but no collector host or endpoint is set.
    * Emit "opentracing_load_tracer <plugin_path> /etc/ingress-controller/telemetry/opentracing.json;\r\n" based on the collector configured.
    * Append "opentracing_operation_name \"<value>\";\n" if `OpentracingOperationName` is set.
    * Append "opentracing_location_operation_name \"<value>\";\n" if `OpentracingLocationOperationName` is set.
* Implement `buildOpentracingForLocation` in `internal/ingress/controller/template/template.go`:
    * Accept a global opentracing enabled flag, a global trust flag, and an `*ingress.Location`.
    * Return "opentracing on;\nopentracing_propagate_context;" when tracing is active and trust is on.
    * Append "opentracing_trust_incoming_span off;" if trust is explicitly disabled.
    * Return an empty string when opentracing is globally disabled and not enabled in the location.
* Implement `shouldLoadOpentracingModule` in `internal/ingress/controller/template/template.go`:
    * Accept a configuration and a servers slice.
    * Return false for invalid input types.
    * Return true if `EnableOpentracing` is true globally or any server location has `Opentracing.Enabled` set to true.
    * Return false otherwise.
* Update the `ingress.Location` struct to include an `Opentracing` field of type `opentracing.Config`.
* Update the `config.Configuration` struct in `internal/ingress/controller/config/config.go` to include fields:
    * `EnableOpentracing` (bool)
    * `JaegerCollectorHost` (string)
    * `ZipkinCollectorHost` (string)
    * `DatadogCollectorHost` (string)
    * `JaegerEndpoint` (string)
    * `OpentracingOperationName` (string)
    * `OpentracingLocationOperationName` (string)

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.