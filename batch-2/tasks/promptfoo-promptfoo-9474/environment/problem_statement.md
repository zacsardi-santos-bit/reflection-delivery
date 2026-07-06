## Description

When promptfoo makes API calls to OpenAI services, it doesn't currently include any identification header to indicate that requests are originating from promptfoo. OpenAI supports a mechanism for client applications to identify themselves in requests, which helps with support, usage analytics, and attribution. Promptfoo should send this identification on all requests to the standard OpenAI API endpoint.

## Expected Behavior

- All requests to the standard OpenAI API (chat completions, completions, embeddings, image generation, moderation, responses, transcription, video generation, real-time WebSocket connections, assistant runs, and ChatKit calls) should include a header identifying the client as promptfoo.
- When a user configures a custom API endpoint (e.g., pointing to a proxy or a compatible third-party service), the identification header should **not** be sent by default, to avoid sending unexpected metadata to non-OpenAI services.
- Users should be able to explicitly override the identification value if needed, and that override should take effect regardless of whether a custom or default endpoint is used.
- The header name and default identification value should be exported as named constants so they can be referenced consistently across the codebase.

## Why This Matters

Without this identification, OpenAI cannot distinguish promptfoo traffic for support purposes. Omitting the header on custom endpoints avoids leaking promptfoo-specific metadata to third-party or proxy services that users may have configured.
