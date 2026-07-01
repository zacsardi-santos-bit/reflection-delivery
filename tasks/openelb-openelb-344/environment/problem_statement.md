## Description

The IPAM (IP Address Management) controller responsible for allocating and releasing external IP addresses to LoadBalancer services currently lacks thorough unit test coverage. The existing tests relied on a full Kubernetes environment and were limited in scope, leaving many important edge cases unverified.

We need comprehensive unit tests — and the corresponding implementation logic — for the three core operations of the IPAM manager:

1. **Deciding what action to take** for a given service (allocate, release, both, or nothing) based on its current state, annotations, and existing EIP pool records.
2. **Assigning an IP** from an EIP pool to a service, including proper error handling for unavailable pools (being deleted, disabled, mismatched protocol, requested IP out of range, pool exhausted) and updating the service's metadata and status.
3. **Releasing an IP** back to the pool when a service no longer needs it, cleaning up the service's metadata.

## Expected Behavior

- When a service is nil or is not a LoadBalancer service with the required annotations, no allocation or release action should be triggered.
- When a service is being deleted or has lost its OpenELB annotations but still has an allocated IP, the manager should produce a release record.
- When switching EIP pools, the manager should produce both a release record (for the old EIP) and an allocation record (for the new EIP).
- IP assignment should fail with an error when the EIP pool is unavailable for any reason (being deleted, disabled, wrong protocol, static IP out of range, pool full).
- After successful assignment, the service should carry the correct finalizer, EIP label, and load balancer ingress IP.
- After successful release, the service should have its finalizer, EIP label, and ingress IP cleared.

## Why This Matters

Without reliable unit tests for these core IPAM operations, regressions in IP allocation logic are hard to catch early. Integration-style tests that require a running cluster are slow and brittle. Proper unit tests with a fake client make it fast and safe to iterate on the IPAM logic.
