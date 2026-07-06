Implement a modification to the Anthropic-compatible API endpoint in vllm to handle inline system messages correctly. Ensure that these messages maintain their original positions and are not merged or reordered, preserving the conversation structure for effective KV-cache usage.

*   Ensure each inline system message remains at its original index in the output message list.
*   Keep multiple inline system messages as separate entries at their respective positions.
*   Place the top-level system prompt as the first message in the output with its content unchanged.
*   Drop any inline system message that consists entirely of 'x-anthropic-billing-header'.
*   For inline system messages with mixed content:
    *   Remove any 'x-anthropic-billing-header' text blocks.
    *   Concatenate the remaining text blocks and emit them as a system message at the original position.
*   Concatenate text blocks of inline system messages without billing headers into a single string and place them at their original position.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.