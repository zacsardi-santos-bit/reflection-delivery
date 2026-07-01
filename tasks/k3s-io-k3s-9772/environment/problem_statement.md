## Description

K3s manages many TLS certificates across its components — server-side services like the API server, scheduler, controller manager, and etcd, as well as agent-side services like the kubelet and kube-proxy. Currently, the mapping from service names to their associated certificate and key files is duplicated or scattered in multiple places (e.g., the certificate rotation logic). There is no single utility that, given a service name or group of services, returns the canonical list of cert/key files for that service.

This makes it difficult to reuse cert-file lookup logic for new features (such as cert expiry checking or reporting), and any new consumer must re-implement the same service-to-file mapping from scratch.

## Expected Behavior

- A new utility package should provide a function that, given a cluster configuration and a list of service names, returns a map of each service to its associated certificate and key file paths.
- Pre-defined service groupings should be available: all services combined, server-only services, and agent-only services.
- A constant for the certificate authority service should be available separately (as it is not part of the standard rotation groups).
- Requesting an unrecognized service name should result in an error rather than silently returning incomplete results.

## Why This Matters

Centralizing this mapping eliminates duplication, reduces the chance of divergence between cert rotation and cert checking logic, and makes it easy to add new certificate management features (like expiry monitoring) without having to re-specify which files belong to which service.
