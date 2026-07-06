## Description

Remote agents that require specific HTTP authentication — such as token-based bearer access, username/password credentials, Digest authentication, or custom proprietary schemes — cannot currently be connected to the system. The only authentication mechanism used when loading and invoking remote agents is a hardcoded default credential flow, regardless of what authentication configuration the user has provided in their agent definition file.

This means that users who configure authentication details for a remote agent (including pulling secrets from environment variables) have their configuration silently ignored, and agents on secured endpoints simply fail to load.

## Expected Behavior

- A user-defined authentication configuration in a remote agent file should be respected when loading and invoking that agent.
- The system should support at minimum: bearer token authentication, HTTP basic (username/password) authentication, and a generic raw-value mode that can be used for other standard HTTP authentication schemes (such as Digest or custom ones).
- Secret values should support dynamic resolution from environment variables so that tokens are not hardcoded in configuration files.
- If the system cannot set up an authentication handler for a configured agent, the agent should be refused (not silently loaded without auth), and a clear warning should be surfaced.
- When no authentication is configured, the agent should be loaded without any auth handler rather than with a default one.

## Why This Matters

Users connecting to remote agents hosted on secured infrastructure have no way to supply credentials today. This gap makes the remote agent feature unusable for any production or protected deployment.
