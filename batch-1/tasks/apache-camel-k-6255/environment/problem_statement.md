## Description

Currently, when deploying an integration that needs to expose services on custom or non-standard network ports, there is no way to declare those ports explicitly through the trait configuration system. The framework automatically manages a single primary HTTP port, but developers are left without a way to configure additional named ports — for example, to expose a service on a custom port number or using UDP instead of TCP.

This lack of flexibility means that integrations requiring non-standard port exposure (e.g., an application listening on port 8085 instead of 8080, or a UDP-based service) cannot be properly configured through the standard trait system.

## Expected Behavior

- Both the container configuration trait and the service exposure trait should accept a list of named port definitions in a simple semicolon-separated string format
- Container ports should support specifying a port name, port number, and optionally a protocol (defaulting to TCP when omitted)
- Service ports should support specifying a port name, the external service port number, the internal container port number, and optionally a protocol
- When an improperly formatted port specification is given, a descriptive error message should be returned explaining the expected format
- When a port number that cannot be parsed as an integer is given, a clear error indicating the problem should be returned
- Custom-declared ports should work alongside any default ports already configured by the framework

## Why This Matters

Without this capability, developers running integrations that need to expose services on non-default ports must use workarounds or cannot properly expose those services at all. Adding explicit port configuration support enables a wider variety of integration patterns, including UDP services and non-standard port bindings, directly through the standard trait configuration interface.
