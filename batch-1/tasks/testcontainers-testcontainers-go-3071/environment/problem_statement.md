## Description

The testcontainers-go library is missing a module for socat — a popular network relay tool used in containerized environments to forward traffic between containers on an internal Docker network. Currently there is no easy way to use testcontainers-go to spin up a socat relay container that routes traffic to services that are only reachable inside a Docker network.

## Expected Behavior

- A new socat module should be available under the modules directory that lets users start a socat relay container.
- Users should be able to configure one or more forwarding targets, each specifying the host to forward to. The exposed port and internal port should both be configurable.
- When the same port is used for both the external listener and the forwarding destination, a simpler constructor should be available.
- When different ports are used (e.g., the relay listens on port 9080 but forwards to port 8080 on the target), a constructor accepting both ports should be provided.
- If the internal port is left unspecified (zero), it should default to the exposed port.
- If the exposed port is zero, attempting to add that target as an option should fail with an error rather than silently producing a broken relay.
- The relay container should expose a method to retrieve the reachable URL for a given forwarded port so callers can make requests through the relay.
- Multiple forwarding targets should be configurable on a single relay container.

## Why This Matters

Integration tests often involve services that are only accessible on Docker-internal networks, not from the host. A socat relay allows test code running on the host to reach those services by forwarding traffic through a container that sits on the same internal network. Without this module, users would need to set up and manage socat containers manually, which is tedious and error-prone.
