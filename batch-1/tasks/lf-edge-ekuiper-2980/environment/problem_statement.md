## Description

The ekuiper stream processing engine currently has no way to accept data from clients over WebSocket connections. Users need a built-in WebSocket server-side data source so that external clients can push messages to the engine, which then routes them into stream processing jobs.

## Expected Behavior

- The engine should be able to start an internal WebSocket server on a configurable IP and port.
- Individual WebSocket endpoints should be registerable and unregisterable at runtime.
- When a connected client sends a message to a registered endpoint, the raw payload should be delivered to any downstream consumers of that endpoint.
- When a client closes its connection, the engine should detect this and cleanly remove the connection from its internal tracking — the count of active connections should drop to zero.
- A higher-level data source abstraction should wrap this server-side WebSocket infrastructure, supporting a full lifecycle: configuration (with validation that an endpoint path is provided), connection establishment, data subscription with a payload callback, and graceful shutdown.
- Configuration with an empty or missing endpoint path should be rejected with an error.
- A connection factory suitable for registration with the engine's connection management system should be provided.

## Why This Matters

Without this capability, users who want to feed real-time data into ekuiper streams from WebSocket clients have no built-in option and must rely on workarounds. Adding a native WebSocket source makes ekuiper a better fit for IoT and event-driven architectures where WebSocket is the preferred transport.
