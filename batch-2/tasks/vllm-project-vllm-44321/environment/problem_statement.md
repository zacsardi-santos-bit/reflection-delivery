## Description

The vllm Rust frontend server currently has no way to restrict access to its inference and model-listing endpoints. Any client that can reach the server can make requests — there is no authentication. We need to add support for API key authentication so that operators can limit access to authorized clients only.

## Expected Behavior

- Operators should be able to configure one or more API keys for the server via a CLI flag (repeatable, so multiple keys are supported) or via the JSON arguments path used by the Python-supervised frontend.
- The JSON arguments path should accept the API key as either a single string or a list of strings.
- When at least one key is configured, requests to the main API routes (e.g., model listing, inference) must include a valid bearer token in the authorization header. Requests missing or presenting a wrong token must be rejected with an HTTP 401 response containing a JSON error body.
- Health check and other auxiliary endpoints must remain accessible without authentication, so monitoring systems can continue to function.
- Browser preflight (OPTIONS) requests must be allowed through without authentication so CORS flows work correctly.
- API key values must never appear in logs or debug output. When the server configuration is printed for diagnostics, the keys should be replaced with a redacted placeholder that only shows the count.

## Why This Matters

Without API key support, the Rust frontend cannot be deployed in environments where request authentication is required. This is a baseline security feature expected for any production-facing inference server.
