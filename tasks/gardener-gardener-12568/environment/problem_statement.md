## Description

In Gardener's seed cluster logging setup, node-level log agents currently push logs directly to the log store (Vali) over a secured endpoint. With the introduction of an OpenTelemetry Collector as an intermediary in the logging pipeline, the system needs to dynamically route log traffic through the collector rather than directly to Vali when the relevant feature is enabled.

This requires several coordinated changes:

- The network ingress rules and access control policies that node log agents rely on should point at the OpenTelemetry Collector (not the log store directly) when the feature is active, and continue pointing at the log store when the feature is disabled.
- The authentication proxy that secures the log ingestion endpoint should be moved from the log store component to the OpenTelemetry Collector component, so the collector owns and manages its own secure endpoint.
- The collector component needs to support toggling the authentication proxy sidecar on or off via a dedicated method, and it needs to accept a secrets manager for credential management rather than a static endpoint string.
- Two utility functions for generating the standard kubeconfig volume and volume mount configurations are needed so that components authenticating against shoot clusters can produce consistent, reusable Kubernetes volume definitions.

## Expected Behavior

- When the OpenTelemetry Collector feature is disabled, the ingress and RBAC rules for log agents continue pointing to the log store directly (existing path and service).
- When the OpenTelemetry Collector feature is enabled, the ingress and RBAC rules for log agents are updated to target the collector service using the Loki-compatible push path.
- The collector component deploys without an authentication proxy by default, and with one when explicitly configured.
- The authentication proxy sidecar (when enabled on the collector) runs with a read-only, non-root security context, exposes port 8080, and mounts a projected kubeconfig volume.
- The log store component no longer activates its own authentication proxy when the logging botanist runs.

## Why This Matters

This change enables the full OpenTelemetry Collector integration in Gardener's logging pipeline, making the collector the secure, central ingestion point for shoot node logs when the feature is turned on.
