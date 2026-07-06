## Description

When the OTEL (telemetry) exporter is configured with an IPv6 host address, the generated endpoint URL is malformed. IPv6 addresses contain colons, which conflict with the colon delimiter used to separate the host from the port in a URL. According to the URL standard, IPv6 literal addresses used in a URL must be enclosed in square brackets. Without this bracketing, the generated URL is unparseable, meaning any deployment that uses an IPv6 address for its metrics or traces collector endpoint will silently use an invalid connection URL.

## Expected Behavior

- When a bare IPv6 address (one containing colons but not already wrapped in brackets) is provided as the OTEL host, the system should automatically wrap it in square brackets when constructing the endpoint URL.
- When an IPv6 address is already enclosed in square brackets, the brackets should be preserved as-is — they must not be doubled or altered.
- When an IPv4 address is provided, it should be passed through to the URL without modification.

## Why This Matters

Users deploying Airflow with IPv6 infrastructure will find that the metrics/traces endpoint URL is generated incorrectly, causing connectivity failures to their telemetry collector. The fix ensures compliance with URL formatting standards so that IPv6-based deployments work correctly out of the box.
