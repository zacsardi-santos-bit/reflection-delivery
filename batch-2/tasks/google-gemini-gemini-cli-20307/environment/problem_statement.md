## Description

When a remote agent fails to load — because its URL is wrong, authentication is not set up, or the network is unavailable — the CLI currently surfaces either a raw, hard-to-read error string or a generic prefixed message that doesn't tell the user what went wrong or how to fix it. The same raw-error problem appears when a remote agent call fails mid-stream. Users are left guessing whether the issue is a bad URL, missing credentials, a firewall, or something else entirely.

## Expected Behavior

- When a remote agent's card URL returns a "not found" response, the user should see a clear message referencing the specific configuration field to update.
- When a remote agent's card returns an authentication or authorization failure, the user should see which HTTP status occurred and be pointed toward the authentication configuration.
- When an agent is loaded successfully but its security requirements are not met by the current configuration, the user should receive a warning that explains what is needed and which config entries are missing — the agent should still be available as a fallback.
- When an unclassified network or connection error occurs, the user should see the underlying failure reason along with the agent card URL.
- Error messages propagating through the streaming layer should preserve the original root cause rather than being re-wrapped with additional prefixes.

## Why This Matters

Without structured, user-friendly errors, debugging a misconfigured remote agent requires reading raw stack traces or source code. These changes let users immediately understand what category of failure occurred and which part of their configuration to fix, dramatically reducing troubleshooting time.
