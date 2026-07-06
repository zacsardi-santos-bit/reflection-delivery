## Description

The session configuration system in Detox needs to be made more flexible and self-contained. Currently, the session configuration requires both a server URL and a session ID to be explicitly provided when any session config is present — omitting either one throws an error. Additionally, a debugging feature that triggers status queries for slow test operations is only configurable via a command-line flag, not through the project configuration file. This creates friction for teams that want to lock in a specific synchronization debugging timeout as part of their committed configuration.

## Expected Behavior

- The session config should have sensible defaults: an auto-generated server URL and session ID should be used when none are provided, so specifying a session block is no longer all-or-nothing.
- A new session-level option should allow developers to specify a time threshold (in milliseconds) after which slow operations trigger diagnostic queries. This should be configurable at the global level, at the per-device level, and overridable via the CLI.
- When a server URL or session ID is explicitly provided, it should be validated for correctness (e.g., a server must be a proper WebSocket address), with clear error messages for invalid values.
- The synchronization debugging timeout configured at the device or global level must be respected at runtime.

## Why This Matters

Teams running end-to-end tests with Detox should be able to fully configure their test sessions through the project config file rather than relying on CLI flags. The current requirement to provide both server and session ID together, or not at all, is overly restrictive. A more forgiving, defaults-based approach with proper validation gives developers better control and clearer feedback.
