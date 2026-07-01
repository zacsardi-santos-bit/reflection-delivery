## Description

Currently, Contour does not support configuring a maximum number of active connections per listener. This means that under high load or adversarial traffic conditions, each listener can accept an unbounded number of connections, which may exhaust resources or leave the system vulnerable to connection-flood attacks.

We need a way to set an optional limit on the number of active connections per listener, applied individually to each listener (e.g., HTTP and HTTPS separately). The limit should be configurable both via the Contour config file and via the Kubernetes custom resource. When no limit is specified, behavior should remain unchanged (unlimited connections).

## Expected Behavior

- Users can configure a maximum connections-per-listener value in the Contour config file under the listener section.
- Users can configure the same setting via the relevant Kubernetes custom resource field on the listener spec.
- The configured limit is enforced per listener independently (HTTP and HTTPS listeners each get their own cap).
- The limit is dynamically applied and removed as listeners are added or removed.
- Setting a value of zero should be rejected as invalid; only values of 1 or greater are accepted.
- When no limit is set, the system defaults to unlimited connections (existing behavior is preserved).

## Why This Matters

Without a connection cap, a single misconfigured client or attacker can exhaust listener capacity. This feature allows operators to enforce connection limits as a protective measure for production deployments.
