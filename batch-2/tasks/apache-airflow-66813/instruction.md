I'm running into an issue with the telemetry exporter configuration in Airflow.

*   When the configured OTEL host is a bare IPv6 address (contains colons but is not already wrapped in square brackets), the constructed endpoint URL must enclose the IPv6 address in square brackets, e.g. a host of '::1' with port '4318' and protocol 'http' must produce 'http://[::1]:4318/v1/metrics'.

*   When the configured OTEL host is a full IPv6 literal address without brackets (e.g., '2001:db8::1'), the constructed endpoint URL must wrap it in square brackets, e.g. 'http://[2001:db8::1]:4318/v1/metrics'.

*   When the configured OTEL host is already enclosed in square brackets (e.g., '[::1]'), the constructed endpoint URL must preserve the existing brackets without doubling them, e.g. 'http://[::1]:4318/v1/metrics'.

*   When the configured OTEL host is an IPv4 address (e.g., '10.0.0.1'), the constructed endpoint URL must pass it through unchanged, e.g. 'http://10.0.0.1:4318/v1/metrics'.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.