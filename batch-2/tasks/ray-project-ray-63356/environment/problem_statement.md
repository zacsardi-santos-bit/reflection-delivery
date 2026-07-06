## Description

Ray Serve's HAProxy integration currently has no way to collect per-request routing metrics. When a request is processed by the ingress request router, there is no visibility into how long the routing decision took, whether the request landed on the intended replica or was redirected to a different one, whether the request body was truncated during routing, or what caused a routing failure. This makes it impossible to build dashboards or alerts around routing quality and latency.

## Expected Behavior

- A new metrics collector component should be introduced that listens for structured log entries from the proxy process over a Unix socket and parses them into typed records.
- The collector should track routing latency (in milliseconds, broken out by success vs. failure outcome), replica mismatch events, truncated body events, per-reason failure counts, and total request counts — all labeled by application name.
- When the application name is not present in a log entry, it should fall back to "unknown" rather than silently dropping the observation.
- Events that did not pass through the router at all should not produce any metric updates.
- The proxy configuration and the associated scripting logic should both support a toggle: when metrics are enabled, the rendered output includes the structured logging directives and timing instrumentation; when disabled, all of that is omitted.
- Binding a socket for receiving log entries should be safe to call even when a stale socket file already exists, and closing the collector should remove the socket file and be safe to call multiple times or before binding.

## Why This Matters

Without routing-level metrics, operators cannot distinguish slow routing from slow replicas, cannot detect replica-pinning failures, and have no signal when body truncation is occurring. This feature makes the ingress request router observable.
