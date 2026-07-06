## Add a dedicated provider for automation workflow webhooks

## Description

We'd like to be able to evaluate automation workflows (specifically n8n workflows) directly within promptfoo, without having to wire up a generic webhook provider manually each time. These workflows expose HTTP endpoints and return responses in a variety of formats, so having a provider that handles them natively would save a lot of boilerplate.

## Expected Behavior

- Users should be able to specify a workflow webhook URL as a provider path (using a dedicated workflow platform prefix before the webhook URL) and have promptfoo route prompts to it automatically.
- The provider should handle the most common response shapes returned by these workflows: responses with a direct output field, a response field, a nested message content field, and array-wrapped item responses.
- Tool calls returned by workflow agents (in either of the two schema formats these workflows may use) should be extracted and exposed as structured metadata on the response.
- Session IDs returned by the workflow should be exposed on the response so multi-turn strategies can manage conversation scope.
- Requests should support configurable HTTP methods, body templates with variable interpolation, and custom headers with environment variable references.
- For non-idempotent methods, retries should be disabled by default to avoid re-delivering workflow side effects.
- The provider should never expose webhook URLs, credentials, or prompt/response content in debug logs — only a safe fingerprinted display identifier should appear.
- The provider ID should be derived from a stable fingerprint of the configuration so that rotating API credentials doesn't fragment historical evaluation results, and so two providers pointing at the same endpoint with different configurations produce distinct IDs.

## Security Fix

There is also a related issue: when HTTP requests fail and are retried, or when connection errors occur, the full URL (including basic-auth credentials and query-string tokens) can appear in debug log output. This affects all providers, not just workflow webhooks. Credentials and sensitive query parameters should be stripped from URLs before they are written to logs, while the hostname should remain visible for debugging.

## Why This Matters

Many teams are building AI-powered automations on n8n and want to evaluate them systematically. Without a native provider, they have to manually configure request shapes and parse responses themselves. The credential-leaking log issue is a security concern since debug logs are often shared in bug reports or stored in CI systems.
