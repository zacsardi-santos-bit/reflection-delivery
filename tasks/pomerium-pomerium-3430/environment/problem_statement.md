## Description

When Pomerium runs behind an Envoy proxy, the real client IP address is not directly available from the incoming connection's remote address. Envoy forwards the original client IP via a dedicated header, but we currently have no shared utility to extract the client IP correctly for all traffic paths.

## Expected Behavior

- A utility function should be available in the HTTP utilities package that returns the client's real IP address from an HTTP request.
- When the proxy-forwarded IP header is present, the function should return that value.
- When the header is absent, the function should fall back to parsing the IP from the connection's remote address (stripping the port if present).
- When neither source is available, the function should default to the loopback address (127.0.0.1).

## Why This Matters

Without a consistent way to determine the client IP, different parts of the codebase may handle this inconsistently or incorrectly when behind a proxy. Centralizing this logic ensures that all handlers can reliably identify the originating client regardless of whether traffic arrives directly or through an Envoy sidecar.
