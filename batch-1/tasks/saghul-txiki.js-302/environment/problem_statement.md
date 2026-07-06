## Description

txiki.js currently provides high-level abstractions for TCP and UDP networking, but there is no way to work with raw POSIX sockets from JavaScript. This limits developers who need to implement custom network protocols, work with socket options at the OS level, or use features that don't map to the existing high-level APIs.

## Expected Behavior

A new low-level socket API should be available on the global runtime object, allowing developers to:

- Create sockets by specifying address family, socket type, and protocol directly
- Access standard socket constants (address families, socket types, option levels, option names) through a single definitions object
- Encode IPv4 addresses and ports into the binary format expected by the operating system
- Bind, send, and receive data on datagram sockets, with the ability to retrieve both the received data and the sender's address
- Set and get socket options with appropriate error reporting when invalid parameters are used
- Create stream (connection-oriented) servers: bind, listen, and accept incoming connections
- Write data to accepted connections and get the byte count back
- Query the socket's underlying file descriptor
- Poll a socket for readability and stop polling on demand
- Convert between network interface names and their numeric indices
- Compute IP checksums over raw byte buffers

## Platform Notes

This functionality is only meaningful on POSIX-compatible platforms. On Windows, the feature should gracefully do nothing rather than raise errors.

## Why This Matters

Without low-level socket access, txiki.js cannot be used for tasks such as raw protocol experimentation, custom datagram protocols, or any application requiring direct control of socket options beyond what the standard networking abstractions expose.
