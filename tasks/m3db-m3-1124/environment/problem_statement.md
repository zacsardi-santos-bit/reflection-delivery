## Description

When issuing time-series queries with a very long time window and a very fine step size, the query engine can end up computing an enormous number of datapoints. There is currently no safeguard to prevent this, which means a single poorly scoped query can exhaust system resources or cause severe performance degradation.

We need a configurable limit on the maximum number of datapoints that a query is allowed to compute. If a query exceeds this limit, it should be rejected immediately with a clear error message that explains what was exceeded and provides actionable guidance — such as reducing the time window, increasing the step size, or adjusting the configured limit.

## Expected Behavior

- The query service configuration should accept a field that controls the maximum number of computed datapoints per query.
- Setting this limit to zero or a negative value should disable enforcement entirely (opt-out).
- When a query would produce more datapoints than the configured limit (computed as the time range divided by the step size), the request should be rejected with an HTTP 400 error and a descriptive structured error response.
- A sample configuration file should demonstrate a working value for this setting.
- All provided example configuration files in the configuration directory should remain valid and loadable.

## Why This Matters

Without this guard, a careless or malicious query could bring down the service. Operators need the ability to put an upper bound on per-query resource usage to protect the cluster.
