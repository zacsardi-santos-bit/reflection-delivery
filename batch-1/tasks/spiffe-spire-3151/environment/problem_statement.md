## Add Health Check Endpoints to the OIDC Discovery Provider

### Description

The OIDC Discovery Provider currently has no way for operators to query its health status. When deploying the provider in a containerized environment, platforms that manage workloads need to know whether the service is alive and ready to serve traffic. Without dedicated health check endpoints, the platform has no signal to route traffic intelligently or restart an unhealthy instance.

### Expected Behavior

- A new optional configuration section should allow operators to enable health check endpoints. When the section is present but empty, sensible defaults should apply automatically for the bind port and the URL paths for each endpoint. Operators should also be able to override these defaults.
- If the health checks section is absent from the configuration, no health check listener should start.
- A **readiness** endpoint should report success only when key material has been successfully fetched within a recent time window. If key material has never been fetched, or if the last successful fetch is too old, the endpoint should report failure.
- A **liveness** endpoint should be more lenient: during initial startup before any key fetch has completed, the service should still report itself as alive. Once key fetching has been established, the liveness endpoint should report failure if polling has gone stale beyond the allowed threshold.
- Both endpoints respond with HTTP 200 for success and HTTP 500 for failure.

### Why This Matters

Kubernetes and similar orchestration systems rely on liveness and readiness probes to route traffic only to healthy instances and to restart instances that become unhealthy. Without these endpoints, the OIDC provider cannot participate properly in automated health management, leading to stale or unreachable instances continuing to receive traffic.
