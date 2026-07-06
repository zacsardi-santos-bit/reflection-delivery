## Description

Every time user account data and project configuration are needed, the authentication setup process makes a fresh network call to the server — even when the same credentials and project were looked up just moments earlier in the same session. This creates unnecessary latency and redundant API calls whenever the same user triggers authentication checks multiple times in quick succession.

## Expected Behavior

- When user setup is called multiple times with the same credentials and project, only the first call should hit the network. Subsequent calls within a short window should return the cached result immediately.
- If the configured project changes between calls, the system must bypass the cache and fetch fresh data.
- After a short expiration window (about 30 seconds), cached results should be considered stale and the system should re-fetch.
- If a previous setup attempt failed with an error, that failure should not be cached — the next attempt should always retry.
- A new general-purpose caching utility should be introduced to power this feature and be available for use elsewhere in the codebase. It should support time-to-live expiration, string and object-keyed storage, automatic eviction of failed asynchronous results, and a "get or create" convenience method.

## Why This Matters

Eliminating redundant network round-trips improves startup time and reduces unnecessary quota consumption. Having a shared caching utility also avoids ad-hoc one-off caching implementations scattered across the codebase.
