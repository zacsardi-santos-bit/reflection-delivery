## Description

The outbound policy controller currently indexes and distributes HTTP routing configurations to proxies, but has no equivalent support for gRPC-specific routing rules. When an operator defines a gRPC route configuration targeting a backend service, that configuration is silently ignored — it never appears in the outbound policy that the controller sends to proxies.

## Expected Behavior

- The outbound policy should include a dedicated collection of gRPC routes alongside the existing HTTP routes.
- When a gRPC routing configuration is applied that references a backend service not yet registered with the controller, the policy should reflect that the backend does not yet exist.
- When the referenced backend service is later registered, the policy should reactively update to indicate the backend is now available.
- Callers subscribed to the outbound policy watch should be notified when the gRPC route backend existence state changes.

## Why This Matters

Without this support, operators who define gRPC routing rules for services have no effect — those rules are never surfaced in the distributed policy. Proxies cannot apply gRPC-specific traffic management until the controller correctly indexes and propagates gRPC route configurations. This gap means gRPC traffic routing is effectively unsupported even when the routing resources are properly defined in the cluster.
