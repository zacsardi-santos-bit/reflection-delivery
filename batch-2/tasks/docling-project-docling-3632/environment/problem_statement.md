## Description

When using an external API endpoint to generate picture descriptions during document processing, the pipeline discards the token usage information returned by the provider. This makes it impossible for callers to track costs, monitor quota consumption, or audit inference usage. There is also no way to handle providers that return usage data under a non-standard key in the response, or under a nested path.

## Expected Behavior

- The response from an API image request should carry the full usage payload (token counts and any provider-specific fields) alongside the generated text and stop reason.
- Callers should be able to specify which key in the provider's JSON response contains the usage data, including dotted paths for nested structures (e.g., a provider that places usage data under a nested path rather than at the top level).
- An alias parameter should be supported for selecting the usage key, for compatibility with existing plugin-style configurations.
- Usage information should flow through all pipeline layers — from the raw HTTP call, through the model wrappers, and into the document metadata on each annotated picture.
- When the pipeline annotates a picture via an API call, the resulting usage payload should be stored on the picture's description metadata and be retrievable by downstream consumers.
- Malformed or empty API responses should be handled gracefully with informative log messages rather than crashing silently.
- The HTTP retry logic for transient failures should be clearly configured and consistent.

## Why This Matters

Users who rely on external vision models for picture annotation often need to track per-document or per-picture token costs for billing and monitoring purposes. Without usage data in the output, they have no programmatic way to attribute costs. Additionally, different providers use different response schemas for usage data, so a flexible key-selection mechanism is needed.
