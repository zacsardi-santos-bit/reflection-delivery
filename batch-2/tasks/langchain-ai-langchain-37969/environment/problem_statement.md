## Description

When building agents with large numbers of tools, sending every tool's full schema on every model call wastes tokens and increases latency. Several AI providers offer a server-side tool search feature that lets the model retrieve a tool's schema on demand rather than receiving all schemas upfront. However, there is currently no built-in middleware to take advantage of this — developers have to manually mark tools as deferred and inject the correct provider-specific search descriptor, which is error-prone and provider-specific.

## Expected Behavior

- Developers should be able to add middleware to an agent that automatically marks selected tools as "deferred" and injects the right provider-side search descriptor for the active provider (currently Anthropic and OpenAI are supported).
- Tools can be identified for deferral either by name, by passing a tool instance, or by pre-marking them at definition time. Any existing metadata on a tool must be preserved when deferral is applied.
- Plain dictionary tools (provider-native tool specs) should pass through unchanged without errors.
- When no tools are deferred, the request should pass through completely unchanged.
- If the middleware cannot determine the provider or the provider doesn't support server-side tool search, it should raise a clear, actionable error instead of silently doing the wrong thing.
- Validation errors for misconfigured tool names should surface before provider-related errors, and should list unknown names in a consistent (sorted) order.
- The middleware should work for both synchronous and asynchronous model calls.

## Why This Matters

Without this feature, using provider-side tool search requires custom boilerplate in every agent, and it's easy to accidentally send the wrong provider format or skip validation. This middleware centralizes the logic, makes it reusable, and provides clear feedback when configuration is wrong.
