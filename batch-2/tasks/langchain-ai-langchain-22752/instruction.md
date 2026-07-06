Implement utility functions in the `messages` module to preprocess conversation histories for chat applications. These functions should handle merging, filtering, and trimming message lists efficiently, without modifying the original data.

*   Implement `merge_message_runs` in `libs/core/langchain_core/messages/utils.py`:
    *   Accept a list of `BaseMessage` objects and return a new list with consecutive messages of the same type merged.
    *   Join string content with newlines, concatenate list-format content blocks and tool calls.
    *   Preserve the ID of the first message in each run; do not merge `ToolMessages`.
    *   Ensure the function does not mutate the input list.
    *   Allow invocation without a message list to return a `Runnable` object with an `.invoke(messages)` method.

*   Implement `filter_messages` in `libs/core/langchain_core/messages/utils.py`:
    *   Accept a list of messages and optional filters (`include_names`, `exclude_names`, `include_ids`, `exclude_ids`, `include_types`, `exclude_types`).
    *   Return only messages that satisfy all provided filters.
    *   Accept type filters as string type names, message class objects, or lists of either.
    *   Ensure the function does not mutate the input list.
    *   Allow invocation without a message list to return a `Runnable` object with an `.invoke(messages)` method.

*   Implement `trim_messages` in `libs/core/langchain_core/messages/utils.py`:
    *   Accept a list of messages, `max_tokens` (int), and `token_counter` (callable).
    *   Trim the list to fit within the token budget.
    *   Support `strategy='first'` to keep messages from the start, and `strategy='last'` to keep messages from the end.
    *   Allow partial message inclusion with `allow_partial=True`, using `text_splitter` for finer granularity.
    *   Always include the first `SystemMessage` with `include_system=True`, reserving its token cost.
    *   Use `end_on` and `start_on` parameters to control message type constraints at the list's end and start.
    *   Ensure the function does not mutate the input list.
    *   Allow invocation without a message list to return a `Runnable` object with an `.invoke(messages)` method.

*   Ensure `filter_messages`, `merge_message_runs`, and `trim_messages` are importable from `langchain_core.messages.utils` and included in the `__all__` list of `libs/core/langchain_core/messages/__init__.py`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.