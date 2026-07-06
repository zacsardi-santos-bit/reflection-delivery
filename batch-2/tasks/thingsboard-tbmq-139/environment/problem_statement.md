## Description

The broker does not currently track which MQTT protocol version a connected client is using, nor which named credential set was used to authenticate the client. Retrieving this information requires expensive lookups on every query, and there is no fast path to access it during an active session.

We need the broker to record each client's protocol version and authentication credential name in dedicated caches when the client connects, and to clean up those entries when the session is fully removed.

## Expected Behavior

- When a client connects, its protocol version (e.g., MQTT 3.1.1 or MQTT 5) is stored in a cache keyed by client ID.
- When a client successfully authenticates using basic credentials, the name of the credential set used is stored in a separate cache keyed by client ID.
- When a non-persistent (clean-session) client disconnects and its session is removed, both cache entries are cleared automatically.
- For persistent sessions, the cached entries remain after the client disconnects and are only removed when the session is fully cleaned up (for example, when the client reconnects with the clean-start flag and then disconnects).

## Why This Matters

This allows the system to quickly retrieve connection details — protocol version and credential name — for any active or persisted session without additional database lookups, improving operational visibility and system performance.
