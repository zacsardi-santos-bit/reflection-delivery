## Description

The Cohere Command reasoning parsers have a bug in how they detect whether a model's thinking/reasoning phase has ended. When processing a full conversation history (where prior turns are included in the token sequence), the current implementation searches the entire sequence for any end-of-reasoning marker. This means a completed reasoning block from a previous conversation turn can cause the parser to incorrectly conclude that the current generation has also finished reasoning — even when the current turn has not generated any reasoning output at all.

## Expected Behavior

- When determining whether reasoning has ended, only the tokens belonging to the **current** model generation should be inspected.
- A special chatbot delimiter token separates the current generation from prior conversation context. The parser should scope its check to only tokens appearing after the most recent such delimiter.
- A prior turn's completed reasoning block should have no effect on whether the current generation is considered to have finished reasoning.
- When no chatbot delimiter is present (i.e., generation-only context with no conversation history), the entire token sequence is considered.

## Why This Matters

Multi-turn conversations with Cohere Command models that support a thinking/reasoning phase can produce incorrect behavior when the reasoning-end detection fails to account for conversation history. This can cause the system to prematurely treat a generation as having completed its reasoning phase when it hasn't, leading to incorrect output routing or early termination of the reasoning step.
