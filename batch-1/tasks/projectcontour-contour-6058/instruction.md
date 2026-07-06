Implement support for limiting the number of active connections per listener in Contour. Configure this feature through both the Contour config file and the Kubernetes custom resource. Ensure that the connection limit is enforced independently for each listener and dynamically updated as listeners change.

*   Update the `ListenerParameters` struct in `pkg/config/parameters.go`:
    *   Add a new optional field `MaxConnectionsPerListener` of type `*uint32`, parseable from YAML with the key `max-connections-per-listener`.
    *   Implement the `Validate()` method to:
        *   Return no error if `MaxConnectionsPerListener` is `nil` or set to a value of at least 1.
        *   Return an error if `MaxConnectionsPerListener` is explicitly set to 0.

*   Modify the `EnvoyListenerConfig` struct in `apis/projectcontour/v1alpha1/contourconfig.go`:
    *   Add a new optional field `MaxConnectionsPerListener` of type `*uint32`, serialized to/from JSON as `maxConnectionsPerListener`.

*   Update the `serveContext.convertToContourConfigurationSpec()` function in `cmd/contour/servecontext.go`:
    *   Copy the value of `Config.Listener.MaxConnectionsPerListener` into the `Envoy.Listener.MaxConnectionsPerListener` field of the resulting `ContourConfigurationSpec`.

*   Enhance the `ConfigurableRuntimeSettings` struct in `internal/xdscache/v3/runtime.go`:
    *   Add a new field `MaxConnectionsPerListener` of type `*uint32`.

*   Modify the `NewRuntimeCache` function in `internal/xdscache/v3/runtime.go`:
    *   Accept a `ConfigurableRuntimeSettings` value that includes `MaxConnectionsPerListener` and store it for use during DAG change processing.

*   Update the `RuntimeCache.OnChange` method in `internal/xdscache/v3/runtime.go`:
    *   Inspect the DAG's listeners and, when `MaxConnectionsPerListener` is set to a value greater than 0, add a runtime field named `envoy.resource_limits.listener.<listener-name>.connection_limit` for each active listener.
    *   Ensure that when there are no listeners, no `connection_limit` fields are added.
    *   Remove any previously computed dynamic connection limit fields when listeners are removed.

*   Ensure the runtime cache Query result:
    *   Includes the field `envoy.resource_limits.listener.ingress_http.connection_limit` with the configured numeric value when a single HTTP listener is active.
    *   Includes both `envoy.resource_limits.listener.ingress_http.connection_limit` and `envoy.resource_limits.listener.ingress_https.connection_limit` with the configured numeric values when both HTTP and HTTPS listeners are active.
    *   Does not include any `envoy.resource_limits.listener.*.connection_limit` fields when there are no active listeners in the DAG.
    *   Always includes the default fields `re2.max_program_size.error_level` (value 1048576) and `re2.max_program_size.warn_level` (value 1000), regardless of listener configuration.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.