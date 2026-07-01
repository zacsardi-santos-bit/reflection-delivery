# Namespace-Scoped EIP Pool Assignment

## Description

Currently, all IP address pools (EIPs) in OpenELB are available to services in any namespace. There is no way to restrict a particular IP pool to specific namespaces, making it impossible for cluster administrators to give different teams or tenants dedicated, isolated IP ranges. Additionally, when multiple pools could satisfy a request, there is no priority mechanism to control which one gets selected.

We also need to handle the case where a service is being deleted but already has an active IP allocation — the system should recognize that an IP release is needed rather than treating it as a no-op.

Finally, the IP allocation record currently stores a protocol field that duplicates information already tracked on the IP pool itself, creating unnecessary constraints that can block valid IP assignments.

## Expected Behavior

- An IP pool should be configurable to serve only specific namespaces, either by listing namespace names directly or by specifying label selectors that match namespace labels.
- When multiple IP pools match a given namespace, the pool with the lowest priority value should be preferred.
- An IP pool can be designated as a cluster-wide default fallback, used by services that don't match any namespace-restricted pool.
- When a service that has an existing IP allocation is being deleted, the system should return a release record for the allocated IP, not silently skip the release.
- IP allocation records should not need a protocol field; the protocol is already stored on the pool itself and should not be used to filter or reject allocation requests.

## Why This Matters

Without namespace-aware IP pool assignment, cluster administrators cannot enforce network isolation between teams. With this feature, operators can create dedicated IP ranges for specific namespaces and ensure that each team's services get IPs from their designated pool, while still providing a sensible default for unconstrained services.
