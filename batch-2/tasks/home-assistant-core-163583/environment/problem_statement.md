## Description

The Anthropic integration stores the model's internal reasoning (thinking) information in conversation history entries as raw API response objects. This creates a mismatch: when those entries are converted back into the format needed for subsequent API calls, the thinking parameters are not correctly reconstructed. Additionally, some edge cases are not handled — for example, when the model produces a thinking block between tool calls (interleaved reasoning), or when a structured data response includes reasoning blocks mixed together with extra text and tool outputs.

## Expected Behavior

- Thinking metadata stored in conversation history entries (including both regular and encrypted reasoning) should round-trip cleanly back to the correct API message format when the conversation history is used in future requests.
- When a model response includes a reasoning block followed by a tool-based structured output, the structured data should be correctly extracted regardless of whether additional text blocks appear between the reasoning and the tool output.
- When the model's reasoning appears after a tool result (interleaved), this should be correctly recorded as part of the conversation and included when constructing messages for subsequent calls.
- When an assistant message consists of a single plain text response with no citations or reasoning, its storage format should be simplified to a plain string rather than a wrapped list structure.

## Why This Matters

These issues can cause incorrect API requests when resuming conversations that involved extended thinking, produce wrong or missing results when using structured data generation with extended thinking enabled, and result in redundant data structures for simple text responses.
