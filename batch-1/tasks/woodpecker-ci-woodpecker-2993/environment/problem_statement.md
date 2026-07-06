## Description

Currently, port definitions in pipeline service configurations only support plain port numbers. There is no way to specify a transport protocol (such as UDP) alongside the port number. This makes it impossible to properly configure services that require non-default protocols — for example, VPN services that operate over UDP cannot be correctly exposed.

## Expected Behavior

- Users should be able to specify ports with an optional protocol in their pipeline YAML configuration, using the format of a port number alone or a port number followed by a slash and a protocol name (e.g., port 51820 with udp protocol).
- The protocol should be preserved and propagated through the backend pipeline systems (Docker, Kubernetes) so that ports are configured with the correct transport protocol.
- Specifying the protocol before the port number (wrong order) or using an unsupported delimiter should be treated as an invalid port definition.
- Kubernetes services and pod containers should expose ports with the correct protocol (uppercased, as Kubernetes requires).
- Kubernetes pod containers should include port entries when ports are defined on a step.

## Why This Matters

Services like WireGuard (UDP) or other non-TCP services cannot be properly exposed in pipeline environments when only the port number can be specified. Adding protocol support enables correct network configuration for these services, ensuring traffic is routed using the right transport protocol.
