## Description

The Perplexity chat integration currently only supports the standard chat completions endpoint, which means developers cannot access Perplexity's more advanced "Responses" (Agent) API. This newer API supports built-in tools like web search, stateful multi-turn conversations using persistent response identifiers, and system instructions — capabilities that are simply not available through the chat completions path.

There is no routing logic today: all requests go to chat completions regardless of whether the payload contains built-in tools or continuation parameters that only the Responses API understands. When a developer passes a built-in tool like web search, the request silently falls back to a path that doesn't support it.

## Expected Behavior

- When a request includes a built-in tool (e.g., web search), the integration should automatically detect this and route to the Responses API instead of chat completions.
- Requests that include conversation continuation identifiers, system instructions, or other Responses-API-only parameters should also be automatically routed to the Responses API.
- Developers should be able to explicitly force or disable the Responses API path at construction time, overriding the auto-detection logic.
- Certain parameters that are incompatible with the Responses API (sampling temperature, stop sequences, tool-choice) must be handled gracefully: silently dropped with a log warning, or rejected with an explicit error so developers are not caught by surprise.
- Streaming and async invocation paths should both be supported through the Responses API with proper usage metadata and error propagation.
- Perplexity-specific extras returned from the Responses API (citations, images, related questions, etc.) should land in the message's supplementary keyword arguments, not in the response metadata.

## Why This Matters

Without this routing layer, developers trying to use built-in tools or multi-turn conversation features have no supported path. Adding auto-detection with clear warnings for incompatible parameters makes the integration both more capable and less surprising to use.
