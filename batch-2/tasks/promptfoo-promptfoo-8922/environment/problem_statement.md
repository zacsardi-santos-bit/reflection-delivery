## Description

The server's API routes lack consistent input validation and standardized error response shapes. When clients send invalid query parameters or malformed request bodies, they may receive raw framework errors or inconsistent JSON rather than clear, actionable responses. Similarly, when resources are missing or internal operations fail unexpectedly, there is no guarantee the response will be well-structured JSON.

## Expected Behavior

- All API endpoints should validate incoming query parameters and request bodies before passing them to downstream services. Invalid inputs should be rejected early with descriptive error responses (HTTP 400) whose JSON bodies identify which field failed validation.
- Missing resources should consistently return HTTP 404 with a structured JSON error body.
- Internal server failures (e.g., when a background service throws) should return HTTP 500 with a structured JSON error rather than an unhandled exception.
- The share URL endpoint should not impose rate limiting on requests.
- The dataset generation endpoint should normalize prompt inputs — both plain string prompts and object prompts missing an explicit display name should be expanded to include both the prompt content and a matching display name before being forwarded to the generation service. Extra fields on object prompts must be preserved.
- The boolean flag for including provider details in results should only be treated as enabled when the query value is the exact string for "true"; other truthy-looking strings such as "1" or "yes" must resolve to false.
- The prompt hash parameter in the URL must be validated as a 64-character hexadecimal string before any database lookup is attempted.
- The telemetry event validation logic and the list of valid event names should be exported from the telemetry module so other parts of the application can reuse them without duplicating logic.
- The server DTO schema file and the telemetry events definition file must be importable as pure modules — importing them must not write to or modify the user's configuration directory.

## Why This Matters

Without consistent validation, bad inputs can propagate into downstream service calls and produce confusing or non-JSON errors for API consumers. Standardizing these response shapes makes the API more predictable and easier to integrate with.
