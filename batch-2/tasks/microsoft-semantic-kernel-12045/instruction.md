Implement a group chat orchestration pattern that allows multiple agents to engage in a structured conversation managed by a coordinator. Develop a default round-robin manager to control agent turns and conversation termination. Ensure the orchestration supports initialization, invocation, real-time observation, and cancellation functionalities.

*   Implement `GroupChatOrchestration` in `python/semantic_kernel/agents/orchestration/group_chat.py`:
    *   Raise `ValueError` during initialization if any agent in the `members` list lacks a description.
    *   Accept parameters: `members` (list of `Agent`), `manager` (`GroupChatManager`), and `agent_response_callback` (optional `Callable`).
    *   Register one `GroupChatAgentActor` per agent and one `GroupChatManagerActor` for the manager.
    *   Add `(number_of_members + 1)` subscriptions to the runtime.
    *   Implement `invoke(task: str | list[ChatMessageContent], runtime: CoreRuntime) -> OrchestrationResult`:
        *   Return `OrchestrationResult` with `get()` method yielding `ChatMessageContent` with role `AuthorRole.ASSISTANT`.
        *   Ensure the number of agent calls equals `max_rounds`.
        *   Accept a list of `ChatMessageContent` as task input, prepending a steering message for each agent call.
        *   Invoke `agent_response_callback` once per agent response, passing `ChatMessageContent` instances.
    *   Implement `OrchestrationResult.cancel()`:
        *   Allow cancellation of in-progress orchestrations.
        *   Raise `RuntimeError` with "The invocation has already been completed." if called post-completion.

*   Implement `RoundRobinGroupChatManager` in `python/semantic_kernel/agents/orchestration/group_chat.py`:
    *   Default fields: `max_rounds=None`, `current_round=0`, `current_index=0`, `human_response_function=None`.
    *   Accept optional `max_rounds` (int) and `human_response_function` (callable) during initialization.
    *   Implement `should_terminate(chat_history: ChatHistory) -> BooleanResult`:
        *   Increment `current_round` each call.
        *   Return `BooleanResult(result=False)` while `current_round <= max_rounds`.
        *   Return `BooleanResult(result=True)` once `current_round > max_rounds`.
        *   Always return `BooleanResult(result=False)` if `max_rounds` is unset.
    *   Implement `select_next_agent(chat_history: ChatHistory, participant_descriptions: dict[str, str]) -> StringResult`:
        *   Return `StringResult(result=<agent_name>)` cycling through `participant_descriptions` keys.
        *   Advance `current_index` after each call, wrapping around as needed.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.