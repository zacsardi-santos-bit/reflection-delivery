## Description

Remote agents sometimes sit behind services that require OAuth2 authorization, but the current agent authentication system only supports API keys and static bearer tokens. Users who want to connect to OAuth2-protected remote agents have no way to configure this, and the system cannot guide them through the browser-based authorization flow, manage tokens, or refresh them when they expire.

Additionally, when fetching agent metadata (the agent card), the system currently passes the authenticated transport fetch to the card resolver — meaning credentials are sent even before knowing whether authentication is required. If the server doesn't need authentication to serve the card, this unnecessarily exposes credentials.

## Expected Behavior

- Users can declare an OAuth2 auth type in a remote agent's configuration file, specifying a client ID, optional client secret, scopes, authorization URL, and token URL.
- The system validates that any URLs provided in the OAuth2 configuration are well-formed, and rejects configurations with invalid URLs.
- When authentication is required, the system initiates the OAuth2 authorization code flow interactively (browser-based), exchanges the code for tokens, and persists those tokens for future sessions.
- Expired tokens are refreshed automatically using any available refresh token. If refresh fails, the system falls back to re-authorization.
- When the user declines to authorize, the system surfaces a clear cancellation message rather than hanging or producing an obscure error.
- For fetching agent cards, the system first tries an unauthenticated request and only falls back to an authenticated request if the server responds with an authentication challenge.

## Why This Matters

Requiring OAuth2 authentication is a common pattern for professionally hosted APIs and agent services. Without this support, users are locked out of an entire class of remote agents. Proper token lifecycle management (storage, refresh, fallback) makes the feature robust for daily use, and the smarter agent card fetching behavior reduces unnecessary credential exposure.
