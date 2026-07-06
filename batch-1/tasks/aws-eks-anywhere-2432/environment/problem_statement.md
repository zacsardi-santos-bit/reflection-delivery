## Description

When deploying packages to EKS Anywhere clusters, there is no way to wait for a Kubernetes service to become ready based on IP address assignment. The existing wait utilities handle other resource conditions but do not support polling a service until it has been assigned a reachable IP address.

## Expected Behavior

- The cluster management tooling should expose a way to wait for a named Kubernetes service (in a given namespace) to be assigned an IP address, whether that is a cluster-internal address or one provided by a load balancer.
- If no valid wait duration is provided, the operation should immediately return an error rather than blocking indefinitely.
- If the service is not assigned an IP within the specified duration, the operation should return a timeout error.
- Once the service has any IP assignment (direct or via load balancer), the operation should complete successfully.

## Why This Matters

Automated workflows and end-to-end tests that deploy services to clusters need to reliably know when a service is ready to receive traffic. Without this capability, tests must either guess at timing or perform ad-hoc polling, which is fragile and error-prone. A robust, timeout-aware wait mechanism for service availability makes cluster automation more reliable.
