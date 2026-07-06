## Description

When a client sends a request using the Anthropic-compatible API where some system instructions are placed inline within the conversation history (rather than in the top-level system field), the server currently extracts all of those inline entries, concatenates them together with any top-level system prompt, and places the merged result at the very start of the message list. This "hoist and merge" behavior rearranges the conversation structure in a way that breaks KV-cache prefix caching — the combined system content no longer matches previously cached states, causing the model to recompute work it could otherwise reuse.

## Expected Behavior

- Inline system messages (system-role entries inside the messages array) should remain at their original positions in the conversation rather than being extracted and placed at the front.
- Multiple inline system messages should each stay as separate, independent entries at their respective positions — they should not be merged together.
- When a top-level system prompt is also provided, it should continue to appear as the first message with its content unchanged; inline system messages in the messages array should not be appended to it.
- Billing metadata injected as inline system messages should be stripped silently: if the message contains only metadata, the message is dropped entirely; if it contains a mix of metadata and real content, only the metadata is removed and the real content is preserved at the original position.

## Why This Matters

Merging and reordering system messages changes the prefix of every subsequent turn in the conversation. This invalidates prefix-cache entries and forces the backend to reprocess content it already computed, hurting throughput and latency. Preserving message positions keeps the conversation structure stable across turns and allows the KV-cache to remain effective.
