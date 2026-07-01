## Description

We would like to add support for traffic mirroring in HTTPProxy routes. Traffic mirroring (also called shadowing) allows operators to duplicate incoming requests to a secondary backend service for testing, debugging, or analysis purposes, without affecting the primary response path.

Currently, all services listed under an HTTPProxy route are treated as primary backends for load balancing. There is no way to designate a service as a shadow/mirror target that receives a copy of traffic silently.

## Expected Behavior

- An operator should be able to mark one of the services in an HTTPProxy route as a mirror target. Requests matched by that route will be sent to the primary services as usual, and a copy will also be forwarded to the mirrored service.
- If more than one service in the same route is marked as a mirror, the configuration should be considered invalid and no routing rules should be produced for that virtual host.
- A route with exactly one mirrored service should continue to produce valid routing configuration that forwards mirrored traffic to the designated shadow service.

## Why This Matters

This feature enables progressive delivery and canary analysis workflows where teams want to observe how a new service version handles real production traffic before directing live users to it. Without mirroring support, teams cannot safely shadow traffic to a new backend using HTTPProxy.
