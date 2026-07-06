## Description

Streamlit's server currently binds to an IPv4-only wildcard address by default, even on systems that support both IPv4 and IPv6. This means that on dual-stack systems, clients connecting via IPv6 (including via the IPv6 loopback address) are unable to reach a Streamlit app unless the user explicitly reconfigures the bind address. Most modern operating systems support listening on both address families simultaneously via a single dual-stack socket, and Streamlit should take advantage of this automatically.

## Expected Behavior

- When the server's bind address has not been explicitly configured by the user and the host system reports IPv6 support, the server should automatically prefer the IPv6 wildcard address, which typically accepts both IPv4 and IPv6 connections.
- If the system reports IPv6 support but the IPv6 bind actually fails at runtime (address family not supported), the server should gracefully fall back to the IPv4 wildcard address rather than crashing.
- Port conflicts (address already in use) should not trigger the IPv4 fallback — they should continue to behave as before (retry or exit based on configuration).
- When the user has explicitly configured the bind address, that configuration must be respected exactly and must not be upgraded or altered.
- The server should detect when it fails to start (rather than silently continuing), and exit with a specific error code in that case.

## Why This Matters

Users running Streamlit in environments that default to IPv6 (such as many Linux containers or cloud environments) may find that their apps are not reachable even though the server appears to start successfully. Automatically binding to both address families where supported eliminates this class of connectivity issue without requiring manual configuration.
