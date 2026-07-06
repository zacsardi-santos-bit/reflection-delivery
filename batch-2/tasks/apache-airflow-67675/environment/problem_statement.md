## Description

The Airflow Helm chart supports Kubernetes Ingress for exposing the API server to external traffic, but it has no support for the Kubernetes Gateway API — a newer, more capable alternative to Ingress that is increasingly adopted in modern clusters. Users who run clusters using the Gateway API need to manually create the appropriate routing resources outside of Helm, which is error-prone and breaks the single-source-of-truth model that Helm provides.

## Expected Behavior

- The chart should be able to optionally render a Gateway API HTTP routing resource for the Airflow API server, controlled by a new enabling flag in the chart's HTTP route configuration for the API server component.
- When enabled, the resource must carry the correct API version and resource kind for the Gateway API.
- Users should be able to configure which gateway the route attaches to (via parent references), including specifying individual listener sections.
- Hostnames should be configurable as a list, and should support Helm template expressions (e.g., using the release name dynamically).
- By default, the route should forward all traffic to the API server backend on its default port using a prefix path match.
- Users should be able to customize the path and path match type, or supply fully custom routing rules that override the default.
- Custom annotations and labels should be supported and merged with component-level and global labels.
- The route must not be rendered when the API server component itself is disabled.
- When a full name override is configured with standard naming enabled, the backend service name in the route must reflect the overridden name.

## Why This Matters

Teams adopting the Kubernetes Gateway API as their cluster's ingress solution have no way to manage the Airflow API server's external routing through the official Helm chart. Adding Gateway API support lets these teams manage all Airflow routing configuration in one place and get the same level of flexibility they have with other chart features.
