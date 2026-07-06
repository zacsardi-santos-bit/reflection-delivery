## Description

The vulnerability scanner currently makes unauthenticated HTTP requests when fetching dependency metadata from Maven and npm registries. This means it cannot access dependencies hosted in private registries that require credentials, silently failing for any project that relies on internally-hosted artifacts.

We need to add proper HTTP authentication support so the scanner can:
- Read credentials configured in Maven settings files (the standard location developers already use to configure their build tools)
- Apply those credentials automatically when communicating with the corresponding registries
- Support multiple authentication schemes: basic username/password, pre-encoded credentials, bearer token, and digest challenge-response

## Expected Behavior

- A new general-purpose HTTP authentication layer should handle the Authorization header for outbound registry requests.
- When configured to always authenticate, the correct header should be sent with the first request.
- When not configured to always authenticate, the system should send an unauthenticated request first, then respond to the server's challenge by selecting the first supported auth method that the server advertises.
- Once a successful authenticated request is made, subsequent requests to the same registry should automatically include the same credentials without re-challenging.
- Basic auth should require both a username and a password (or a pre-encoded credential token) — if only one field is present, it should not attempt auth.
- Maven settings files should be parsed to extract server credentials, with environment variable reference placeholders expanded to their corresponding environment variable values. If an environment variable is not set, the placeholder should be left unchanged.
- User-level Maven settings should take precedence over global-level Maven settings when credentials exist for the same server ID.
- npm registry requests should apply authentication at the time of making the request, rather than separating request construction from execution.

## Why This Matters

Many organizations host their internal artifacts in private Maven or npm registries. Without authentication support, the scanner cannot follow dependency chains into these private registries, producing incomplete or incorrect vulnerability reports for any project with private dependencies.
