## Description

The OpenTelemetry Operator should automatically manage the Kubernetes RBAC resources required by the collector based on its configuration. Currently, when users configure processors that need access to Kubernetes or OpenShift APIs — such as resource detection processors — they must manually create the necessary cluster roles and bindings themselves. This is error-prone and a poor user experience.

## Expected Behavior

- When a collector is configured with a resource detection processor that uses the Kubernetes detector, the operator should automatically generate a cluster role granting read access to node resources (get, list on nodes) and the corresponding role binding.
- When a collector is configured with a resource detection processor that uses the OpenShift detector, the operator should automatically generate a cluster role granting read access to OpenShift infrastructure resources (get, watch, list on infrastructures and infrastructures/status) and the corresponding role binding.
- When a collector configuration does not require any Kubernetes API access (e.g., it only uses standard exporters/receivers with no resource detection), no cluster role or role binding should be generated.

## Why This Matters

Users deploying OpenTelemetry Collectors with resource enrichment capabilities should not need to understand the underlying Kubernetes RBAC model to get their collectors running. The operator is in the best position to derive the required permissions from the collector configuration and manage them automatically, reducing manual toil and configuration errors.
