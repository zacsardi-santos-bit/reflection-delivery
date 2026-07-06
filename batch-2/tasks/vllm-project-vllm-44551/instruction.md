Implement a fix for the reasoning-end detection in the Cohere Command reasoning parsers to ensure that only the current model generation's tokens are considered. Update the method to correctly handle multi-turn conversation contexts by using a special delimiter token.

*   Update the `is_reasoning_end` method in `BaseCohereCommandReasoningParser` to:
    *   Scope the reasoning-end check to only the tokens belonging to the current model generation.
    *   Consider only tokens after the last occurrence of the chatbot marker token (`<|CHATBOT_TOKEN|>`).
    *   Consider the entire sequence if no chatbot marker token is present.
    *   Return `True` if the relevant token window contains a complete reasoning block, defined as an `end-thinking` token paired with a preceding `start-thinking` token within that window.
    *   Return `False` if the relevant window does not end with a complete reasoning block.

*   Update the `BaseCohereCommandReasoningParser` constructor to:
    *   Initialize and store `chatbot_token_id` using `tokenizer.convert_tokens_to_ids('<|CHATBOT_TOKEN|>')`.
    *   Initialize and store `start_token_id` using `tokenizer.convert_tokens_to_ids('<|START_THINKING|>')`.
    *   Ensure `end_token_id` is already stored from `'<|END_THINKING|>'`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.