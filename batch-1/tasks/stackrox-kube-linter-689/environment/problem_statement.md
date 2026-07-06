## Description

The kube-linter tool currently validates that a container's liveness probe references a port that is actually exposed by the container, but this validation is incomplete in two ways:

1. **Missing probe types**: Readiness probes and startup probes are not checked at all. A misconfigured readiness or startup probe that references an unexposed port goes undetected.
2. **Missing probe protocol**: The newer gRPC-based health probe mechanism is not validated for any of the three probe lifecycle types. If a container is configured with a gRPC health probe pointing to a port number that isn't in the container's exposed ports list, the linter silently accepts this misconfiguration.

## Expected Behavior

- The linter should warn when a liveness probe uses a gRPC health check that references a port number not exposed by the container.
- New lint checks should be introduced for readiness probes and startup probes, analogous to the existing liveness probe check. These should detect when any of the supported probe mechanisms (HTTP, TCP socket, or gRPC) reference a port that is not exposed.
- When no probe is configured, or when the probe uses a command-execution-based action, no warning should be emitted.

## Why This Matters

Misconfigured health probes can cause Kubernetes to either never mark a pod as ready or to fail liveness checks unexpectedly. Catching port mismatches at lint time — before deployment — helps developers identify these problems early. Extending coverage to all three probe types and to the gRPC probe mechanism closes a significant gap in the current health-check validation.
