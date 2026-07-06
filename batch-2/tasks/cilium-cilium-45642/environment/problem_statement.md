## Description

When zone-aware traffic routing is configured to prefer backends in the same availability zone as the node, there is a gap in the fallback logic: if all backends in the local zone are flagged as unhealthy by a health checker, the routing system still restricts traffic to that zone instead of falling back to healthy backends in other zones.

This means that a service configured for zone-preferred routing can effectively be stuck sending requests to known-bad backends when a zone-local outage or health degradation occurs. Backends in other zones that are perfectly healthy remain unused.

## Expected Behavior

- When zone-preference routing is active and health checking is enabled, backends in the local zone that are marked as unhealthy should not be treated as valid candidates for zone-preference.
- If all same-zone backends are unhealthy, the routing logic should fall back to using all available backends across all zones.
- Healthy backends in remote zones must be included in the selection when no healthy local-zone backends exist.

## Why This Matters

Without this fix, a partial zone failure (where all local backends become unhealthy but remote backends are healthy) can cause service degradation even though sufficient capacity exists in other zones. The zone-preference feature should be a best-effort optimization, not a hard restriction that prevents failover.
