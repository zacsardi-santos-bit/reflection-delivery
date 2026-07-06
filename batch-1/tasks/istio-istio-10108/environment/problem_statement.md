## Description

Istio's control plane is not correctly implementing the route discovery subscription protocol when communicating with Envoy proxies. There are two related issues:

**1. Subscription-based push filtering is broken.**
When a global configuration push occurs, every connected proxy receives all available routes — regardless of which routes each proxy subscribed to. A gateway that subscribed to only one specific route should continue to receive only that route on subsequent pushes, but instead it receives everything. This wastes bandwidth and violates the protocol contract.

**2. Protocol error recovery and nonce validation are not reliable.**
The nonce-based acknowledgment protocol is not handled correctly. When a proxy sends a stale or mismatched nonce, the server should detect and ignore that request rather than processing it. When a proxy signals a protocol error (a nonce-based acknowledgment with no route names), the server should log the error and continue — subsequent fresh subscription requests from the same connection must still be served correctly.

## Expected Behavior

- When Envoy subscribes to a specific set of routes, any subsequent push must only deliver those subscribed routes, not all routes.
- When Envoy changes its route subscription, the server must respond with only the updated set.
- Stale acknowledgment tokens (nonces that do not match the last sent token) must be silently ignored.
- After a protocol error, the server must recover and serve subsequent fresh route requests normally.

## Why This Matters

Envoy gateways often need only a small subset of all available route configurations. Sending all routes on every push is inefficient and can cause incorrect proxy behavior. Proper protocol compliance — including nonce tracking, subscription filtering, and error recovery — is essential for correct and efficient communication between Pilot and Envoy.
