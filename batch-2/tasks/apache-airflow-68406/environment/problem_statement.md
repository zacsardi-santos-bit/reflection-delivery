## Description

The Airflow Helm chart currently supports traditional Kubernetes Ingress resources for routing HTTP traffic to the Airflow API server. However, Kubernetes has introduced a newer networking standard — the Gateway API — which uses a different type of routing resource. Users running Airflow on clusters that have adopted this newer standard cannot currently configure routing for the API server through the official Helm chart values.

We need to add a new optional Helm template that creates a Gateway API-compatible HTTP routing resource for the Airflow API server. When enabled via chart values, the template should allow users to:

- Configure parent gateway references (including optional section names)
- Specify one or more hostnames (with support for dynamic name templating based on the release)
- Control how incoming requests are matched by path and what backend they are routed to
- Override routing rules entirely with custom definitions
- Add annotations and labels to the generated resource

## Expected Behavior

- The new routing resource should only be rendered when explicitly enabled in chart values
- It should also be suppressed if the API server component itself is disabled
- Default routing should use a path-prefix match on the root path pointing to the API server service at port 8080, with the service name derived from the Helm release name
- Labels should be merged from global chart labels, API server-specific labels, and route-specific labels — with standard release and component labels always present
- The rendered resource must pass Kubernetes Gateway API schema validation

## Why This Matters

Teams migrating their cluster networking to the Gateway API standard currently must manually create routing resources outside of Helm, making Airflow harder to operate and upgrade on modern Kubernetes clusters. First-class support in the Helm chart keeps configuration centralized and upgrades seamless.
