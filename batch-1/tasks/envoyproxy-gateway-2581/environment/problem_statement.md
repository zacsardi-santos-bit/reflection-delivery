## Description

Envoy Gateway allows operators to configure TCP keepalive settings for upstream connections through backend traffic policies. These settings — including the number of keepalive probes, the idle time before probing begins, and the probe interval — are accepted by the API and stored in the internal representation, but they are never forwarded to the underlying Envoy cluster configuration. As a result, the proxy always makes upstream connections without any keepalive behavior, regardless of what the policy specifies.

## Expected Behavior

- When a route has TCP keepalive settings configured in the intermediate representation, those settings should be translated into the corresponding Envoy cluster's upstream connection options.
- The translated cluster configuration should include the probe count, idle time, and probe interval values as Envoy keepalive parameters.
- A new xDS translation test case covering upstream TCP keepalive should pass, validating the full round-trip from IR input to expected cluster YAML output.

## Why This Matters

Long-lived upstream connections can silently drop without TCP keepalive enabled. Operators who have configured keepalive policies expect those settings to actually be applied at the proxy level. Until this translation step exists, the policy has no effect on the running system, which can lead to hard-to-diagnose connectivity failures with backend services.
