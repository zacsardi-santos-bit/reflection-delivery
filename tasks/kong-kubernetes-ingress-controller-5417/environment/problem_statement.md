## Description

The upstream policy conflict detection logic incorrectly flags a policy as conflicted when two services with different upstream policies are routed by the same HTTP route — even if those services appear in completely separate routing rules.

Currently, the system checks all backend references across all rules in an HTTP route when determining if an upstream policy has a conflict. This means that if route rule 1 references service A (with policy X) and route rule 2 references service B (with policy Y), the system incorrectly marks policy X as conflicted because service B (which uses a different policy) is part of the same HTTP route.

## Expected Behavior

- A conflict should only be detected when two or more backend services with different upstream policies appear together **within the same routing rule**.
- When services with different upstream policies appear in **separate rules** of the same HTTP route, there should be no conflict, and the policy status for each service should be "Accepted".
- When a service is the only backend in its rule, it should always be considered non-conflicting regardless of what appears in other rules.

## Why This Matters

An HTTP route can have multiple independent rules, each routing traffic to different services. There is no functional conflict in having different upstream policies on services that are in completely separate rules, because Kong can only apply a single upstream policy per upstream anyway. The current overly broad conflict detection incorrectly rejects valid configurations, preventing operators from using per-service upstream policies across multiple rules of the same route.
