## Description

The OpenAI Python client now includes a new Responses API endpoint for interacting with language models. However, the existing OpenTelemetry instrumentation for OpenAI only covers the older chat completions interface — calls made through the newer Responses API generate no traces or spans at all. This is a gap in observability coverage.

## Expected Behavior

- Calls to the Responses API should produce an OpenTelemetry span with the same level of detail as existing instrumentation for the completions API.
- The span should capture the system name, requested model, and the actual model version used in the response.
- Input prompts should be recorded in the span, whether the input is a simple text string or a structured list of conversation messages.
- For multi-turn conversation inputs, each message in the list should be recorded with its role and content, with complex content structures JSON-encoded.
- The text output should be recorded as a completion in the span.
- When the request includes tool/function definitions, those definitions should be captured in the span along with any tool calls in the response (including the function name, arguments, and call identifier).
- The response identifier from the API should also be stored on the span.

## Why This Matters

Developers using the newer Responses API have no visibility into these requests through their observability stack. Without instrumentation, there is no way to monitor, debug, or analyze AI calls made through this endpoint. Adding instrumentation brings the new API to feature parity with the existing coverage.
