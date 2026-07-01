## Description

We need a dedicated connector service that acts as a gateway allowing external clients (such as dApps) to route raw RPC calls through the node. Currently there is no structured service layer for this purpose, so there is no clean way for connectors to proxy calls to the underlying RPC infrastructure.

## Expected Behavior

- A new connector service exists as its own package and can be enabled or disabled via a boolean flag in the node configuration.
- The node configuration persists the connector's enabled/disabled state to the database alongside the rest of the node's settings.
- The service follows the standard lifecycle pattern used by other node services: it can be started and stopped cleanly (both operations should succeed without errors).
- The service exposes a versioned RPC namespace through the node's RPC layer.
- The service exposes no peer-to-peer protocols.
- Through the RPC namespace, callers can submit raw RPC requests. Valid Ethereum methods are forwarded successfully, and requests for unknown or unavailable methods return a response indicating the method does not exist or is not available.

## Why This Matters

Without this service, there is no standard, versioned entry point for dApp connectors to interact with the underlying node's RPC capabilities. Adding this service establishes a clear, controlled channel for dApps to issue RPC calls while respecting the same enable/disable configurability as other node services.
