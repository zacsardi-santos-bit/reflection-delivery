## Description

When connecting to an MCP server that doesn't support prompt listing, the server responds with a standard "method not found" error. The current code detects this by checking if the error message text contains a specific phrase. This approach is fragile: if the server returns the correct typed error but with a different message, the check fails and a spurious diagnostic is emitted — making it appear something went wrong when the server is actually behaving as expected.

## Expected Behavior

- When a server responds to a prompt discovery request with a "method not found" error (using the proper MCP error type and error code), the discovery should silently return an empty list of prompts.
- No diagnostic or warning should be emitted in this case — the server is simply indicating it does not support the feature.
- The detection logic should rely on the error's type and code, not on the content of the error message string.

## Why This Matters

Relying on error message text is brittle. As long as the server returns the appropriate typed error code, the client should recognize it correctly and remain silent. This eliminates false-positive warnings and makes the integration more robust against servers that use the proper error code but vary the human-readable message text.
