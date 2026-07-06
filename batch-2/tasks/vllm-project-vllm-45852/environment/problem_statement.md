## Description

When a thinking-capable model is used in a multi-turn conversation that involves tool calls, the chat template can leave the prompt ending in the middle of an open reasoning channel. Specifically, after a tool response and with thinking enabled, the prompt ends right after the reasoning channel has been opened — but before it has been closed. The model then continues generating text inside that already-open reasoning channel.

The problem is that the streaming parser doesn't know the prompt ended in this state. It assumes a default starting state (regular content), so the first tokens the model generates — which are reasoning/thinking tokens — get classified as visible content rather than as reasoning. This causes internal thinking to leak into the response content that the user sees.

## Expected Behavior

- When the prompt ends inside an open reasoning channel, the parser should detect this and start in the correct state so that generated tokens before the reasoning close marker are classified as **reasoning**, not content.
- Tokens after the reasoning close marker should continue to be classified as **content**, as expected.
- If the model also redundantly emits a reasoning channel opener (even though the parser is already in reasoning mode), that redundant marker should be silently discarded and not appear as text in either the reasoning or content output.
- Existing behavior for normal (non-open-channel) prompts must be fully preserved.

## Why This Matters

Users relying on tool-augmented multi-turn conversations with thinking-enabled models see garbled responses where internal reasoning appears as if it were the model's final answer. This is a correctness regression for the post-tool-call continuation pattern.

Regression tracked as vllm-project/vllm#45834.
