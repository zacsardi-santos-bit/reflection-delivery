## Description

pnpm currently treats "ping" as a not-yet-implemented command and will reject attempts to use it. However, testing connectivity to a registry before performing installs or publishes is a common need — both for debugging network issues and for verifying that authentication is set up correctly. Other package managers provide this functionality natively, and users expect pnpm to do the same.

## Expected Behavior

- Running the ping command against a registry should display the registry URL and the round-trip response time in milliseconds.
- If the registry returns additional data in its response, that data should be shown alongside the timing output.
- If no specific registry is given, the command should fall back to the configured default registry.
- Registry URLs that include a path prefix (but no trailing slash) must be handled correctly — the connectivity check must be sent to the right endpoint under that prefix.
- If the registry cannot be reached — whether due to a network failure or an unexpected HTTP response code — the command must report a clear error indicating the registry was unreachable.

## Why This Matters

Without a native ping command, users have no easy way to verify connectivity to their registry from within the pnpm toolchain. This makes troubleshooting slow or failing installs harder, especially in environments behind custom registries or proxies.
