## Description

Some LLMs generate internal "reasoning" or "chain-of-thought" content before producing their final visible answer. While this can be useful for debugging, many production use cases don't want to expose the internal reasoning to end users — they only want the final answer.

Currently, the chat completions API rejects any non-streaming request that explicitly opts out of receiving reasoning content, treating this opt-out as an unsupported feature. This means developers cannot suppress reasoning output in non-streaming responses at all.

## Expected Behavior

- A non-streaming chat completion request with reasoning suppressed should be accepted and return a successful response.
- The response message should contain only the visible answer text, with no reasoning content included.
- When reasoning is suppressed, any per-token output metadata (such as log probabilities and output token IDs) should also be omitted from the response choices, since that metadata would otherwise expose the hidden reasoning tokens through a side channel.
- Prompt-level metadata (such as the prompt token IDs) should still be present in the response even when reasoning is suppressed.

## Why This Matters

Developers building user-facing applications on top of reasoning-capable models need a way to keep internal chain-of-thought private while still using the final answer. Without this capability, they must either expose internal reasoning to users or use a separate post-processing step to strip it — neither of which is ideal. The server should handle this cleanly at the API level.
