## Description

When connecting to an MCP server that requires OAuth authentication, the current implementation fetches a single access token at connection setup time and freezes it inside the HTTP transport. As soon as that token expires, all subsequent requests to the server fail with authentication errors — the transport has no way to refresh or re-acquire a valid token because the credential was captured once at startup.

There is also a secondary issue: even servers whose stored OAuth tokens haven't expired are using this "snapshot" approach, which means tokens cached in memory can outlive their actual validity window with no mechanism for refresh.

## Expected Behavior

- When an MCP server has stored OAuth credentials, the transport should use a dynamic token provider that can look up and return a fresh valid token on demand at request time, rather than locking in a single token at connection time.
- If the stored token includes an expiration timestamp, the provider should cache the result in memory to reduce redundant lookups, but only for as long as the token remains valid.
- If the stored token does not include an expiration timestamp, the provider must re-fetch the token on each request so stale credentials are never indefinitely reused.

## Why This Matters

Long-running sessions are broken today as soon as an OAuth token expires mid-session. The dynamic approach keeps connections alive across token renewals without requiring the user to restart the CLI.
