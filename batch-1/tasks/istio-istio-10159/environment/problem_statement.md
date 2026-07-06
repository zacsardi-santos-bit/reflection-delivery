## Description

Istio's cluster generation does not currently propagate TCP keepalive settings into the Envoy cluster configurations it produces. Operators who configure TCP keepalive at the global mesh level or per-destination traffic policy have no way to ensure those settings are reflected in the upstream cluster definitions that Envoy uses.

## Expected Behavior

- When TCP keepalive is configured globally in the mesh configuration, all generated outbound clusters should have the corresponding upstream connection keepalive settings applied.
- When a destination traffic policy specifies TCP keepalive values for a port, those values should override the global mesh-wide keepalive in the generated cluster.
- When a destination traffic policy explicitly sets an empty keepalive configuration, the cluster should carry a keepalive entry with no specific values set, allowing the underlying OS to use its own defaults. This effectively overrides the global keepalive setting without specifying explicit values.
- When no keepalive settings exist anywhere, the upstream connection options in the cluster should be absent entirely.

## Why This Matters

Without this support, TCP keepalive configuration in mesh and traffic policies is silently ignored — connections may remain stale in certain network environments and operators have no programmatic way to tune keepalive behavior at the Envoy cluster level. This feature gives operators proper control over TCP connection health for their services.
