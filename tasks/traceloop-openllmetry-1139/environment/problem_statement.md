## Description

There is currently no OpenTelemetry instrumentation package for the Mistral AI client library. Developers using Mistral AI for chat completions or embedding generation have no automatic way to capture trace data for these calls, making it difficult to observe, debug, or monitor AI workloads in their distributed systems.

We need a new instrumentation package that automatically wraps Mistral AI client calls in OpenTelemetry spans, capturing key information about each request and response — including the model used, input prompts, output completions, token usage, and whether the call was streaming or not.

## Expected Behavior

- When an application makes a chat completion request to Mistral AI (synchronous or asynchronous, streaming or non-streaming), a trace span should be created capturing the request model, user prompt, assistant response, and token usage.
- When an application makes an embeddings request, a trace span should be created capturing the request model and input text.
- Streaming responses should be detected and the span should reflect that streaming was used, with the full assembled response captured in the span.
- Token counts (prompt tokens, completion tokens, and total tokens) should be recorded on chat spans.

## Why This Matters

Without this instrumentation, teams using Mistral AI cannot observe their AI calls as part of their broader distributed traces. Adding automatic instrumentation means developers get complete visibility into their Mistral AI usage — latency, token costs, inputs, and outputs — without any changes to their existing application code.
