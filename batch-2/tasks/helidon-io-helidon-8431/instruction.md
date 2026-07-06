Implement the `OciMetricsBean` class to enable subclassing and customization of OCI metrics integration. Ensure that key lifecycle methods are accessible and that initialization order is correct.

*   Modify `OciMetricsBean` to allow subclassing:
    *   Implement a protected method `configKey()` that returns a `String` used to look up OCI metrics settings. Default should return "ocimetrics".
    *   Implement a protected method `ociMetricsSupportBuilder(Config rootConfig, Config ociMetricsConfig, Monitoring monitoring)` that returns an `OciMetricsSupport.Builder` initialized with the provided parameters.
    *   Implement a protected method `activateOciMetricsSupport(Config rootConfig, Config ociMetricsConfig, OciMetricsSupport.Builder builder)` to build and store an `OciMetricsSupport` instance, and register it using the routing builder derived from the OCI metrics config node.
    *   Implement a package-private method `ociMetricsSupport()` that returns the `OciMetricsSupport` instance created during activation, or null if not activated.

*   Ensure correct initialization order:
    *   Implement the CDI observer method `registerOciMetrics(@Observes @Priority(LIBRARY_BEFORE + 20) @Initialized(ApplicationScoped.class) Object ignore, Config rootConfig, Monitoring monitoringClient)`.
    *   Ensure the `@Priority` value on the observer parameter is strictly greater than the `@Priority` value on the `@Observes` parameter of `MetricsCdiExtension`'s server-registration observer method.

*   Enable extensibility via CDI @Alternative subclassing:
    *   Allow subclasses to override `configKey()` and `ociMetricsSupportBuilder()` to customize configuration key and builder initialization.
    *   When a subclass is active, ensure its overridden methods are called during the standard startup lifecycle.
    *   If `configKey()` is overridden to return "oci.metrics", retrieve the OCI metrics config node from the root config at that key, and ensure the `OciMetricsSupport` reflects config values from that key.

*   Ensure `OciMetricsSupport` includes:
    *   Instance fields `namespace` and `resourceGroup` of type `String`, reflecting values configured via the builder.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.