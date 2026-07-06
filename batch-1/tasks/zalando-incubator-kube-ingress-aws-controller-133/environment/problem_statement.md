## Description

Currently, the ingress controller automatically aggregates all ingresses under as few Application Load Balancers as possible. There is no way to indicate that a specific ingress should have its own dedicated load balancer rather than sharing one with other ingresses.

We need a mechanism for users to opt a particular ingress out of ALB sharing, so the controller provisions a unique load balancer exclusively for that ingress.

## Expected Behavior

- Ingresses should support an annotation that controls whether the ingress participates in ALB sharing.
- When the annotation is absent or set to indicate sharing is allowed, the ingress should be grouped with others as normal (default behavior).
- When the annotation is set to opt out of sharing, the ingress should get its own dedicated load balancer.
- The internal representation of an ingress must carry this shared/exclusive flag so that it can be read back correctly (roundtrip fidelity must be maintained).

## Why This Matters

Without this capability, operators have no control over ALB assignment for individual ingresses. Some workloads require isolation for security, traffic management, or cost allocation reasons. This annotation gives operators the flexibility to mark individual ingresses as exclusive when needed, while preserving the default shared behavior for the rest.
