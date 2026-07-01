## Description

BuildKit's socket forwarding mechanism currently only supports forwarding sockets that speak the SSH agent protocol. When a socket is forwarded into a build container, the system always wraps it with SSH agent protocol handling. This makes it impossible to forward arbitrary Unix domain sockets — such as an HTTP server, a database listener, or any custom IPC service — into the build environment without adding protocol translation.

## Expected Behavior

- A "raw" forwarding mode should be available so that any Unix domain socket on the build host can be forwarded directly into a build container without SSH agent protocol wrapping.
- The command-line tool for invoking builds should accept a raw mode toggle option alongside the socket path argument. The toggle can appear in any order relative to the socket path.
- The programmatic API's socket configuration struct should include a corresponding boolean field to enable raw forwarding.
- Validation must reject raw mode when no socket path is provided, or when more than one socket path is specified.
- Validation must reject raw mode when the provided path is a regular file rather than an actual Unix domain socket.

## Why This Matters

Developers sometimes need to expose services running on Unix sockets (like local HTTP APIs, proxies, or custom tooling) into their build steps. Without raw socket forwarding, those services cannot be reached from inside a build container without extra protocol adaptation. This feature enables a straightforward way to bridge arbitrary host sockets into the build environment.
