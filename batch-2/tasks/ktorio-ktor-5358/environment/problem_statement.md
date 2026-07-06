## Description

The server-side session system currently does not support two important capabilities that are commonly needed in production applications:

1. **Custom session ID generation based on request context**: Session IDs are always generated using a built-in random generator. There is no way to incorporate request-specific information — such as device fingerprints, user agent strings, or other headers — into the session ID generation logic.

2. **Clearing a session by ID without an active request**: Developers can only clear the session that is currently associated with the incoming request. There is no way to programmatically invalidate a specific session by its identifier. This makes it impossible to implement patterns like "sign out from all devices" or "revoke a specific session," which require removing sessions that belong to other requests or devices.

## Expected Behavior

- When configuring a session cookie or header, developers should be able to provide a custom ID generation function that receives the current request and returns a string to use as the session ID.
- There should be a way to remove any session from server-side storage by providing its ID string, regardless of which session is currently active in the request.
- Clearing one session by ID must not affect any other active sessions.
- Attempting to clear a session by ID for a provider that does not use server-side storage should result in a clear error.

## Why This Matters

These features are essential for multi-device authentication flows. Without request-aware ID generation, each device looks identical from the session system's perspective. Without the ability to revoke sessions by ID, there is no safe way to implement exclusive login (logging in from a new device logs out all other devices) or administrative session management.
