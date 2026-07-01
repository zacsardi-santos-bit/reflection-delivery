## Description

The k8sgpt integration system already supports third-party tools (like Trivy for vulnerability scanning), but Prometheus is not currently included as a supported integration. Users running Prometheus in their Kubernetes clusters cannot retrieve it by name or see it listed among available integrations. This makes it impossible for users or downstream tooling to interact with Prometheus through k8sgpt's integration API.

## Expected Behavior

- Prometheus should appear in the list of available integrations when listing all registered integrations.
- Users should be able to retrieve the Prometheus integration by name without errors and receive a valid, non-nil integration object.

## Why This Matters

Prometheus is one of the most widely used monitoring tools in the Kubernetes ecosystem. Adding it as a first-class integration allows k8sgpt to discover and analyze Prometheus-related resources in a cluster, consistent with how other integrations work. Without this, users get no Prometheus coverage from the integration layer at all.
