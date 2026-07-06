## Description

We need to support conditional rate limiting based on JWT token claims in the NGINX Kubernetes Ingress Controller. Currently, rate limit policies apply uniformly to all requests, but many real-world use cases require different rate limits for different user tiers or roles as identified by their JWT tokens.

For example, an operator might want to allow "gold" tier users 100 requests per second while limiting "silver" tier users to 20 requests per second. Today there is no way to express this — rate limits apply to all matching traffic regardless of who the requestor is.

## Expected Behavior

- Rate limit policies should support an optional condition that checks whether a specific JWT claim matches a specific value
- When the condition is met, the rate limit applies; when it is not, the request is handled by a different policy or passes through unconstrained
- When multiple policies reference the same JWT claim, the system should deduplicate the generated configuration entries to avoid redundancy
- JWT-based conditions must only be accepted for the commercial version of NGINX — specifying such a condition for the open-source version should produce a clear validation error
- A condition block that is declared but does not include a JWT sub-condition should also be rejected with a validation error

## Why This Matters

This feature lets platform teams implement tiered access control at the rate-limiting layer without requiring changes to application code. It's a common requirement for API gateways and multi-tenant services where different subscribers have different usage entitlements encoded in their JWT tokens.
