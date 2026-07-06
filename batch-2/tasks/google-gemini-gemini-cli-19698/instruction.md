Implement a system to manage session visibility and error messaging for a multi-agent tool. Ensure that sessions created by sub-agents are hidden from the user-facing session list, and improve error message readability during initialization failures.

*   Update the `ConversationRecord` interface in `packages/core/src/services/chatRecordingService.ts`:
    *   Add an optional field `kind` with allowed values `'main'` or `'subagent'`.

*   Modify the `ChatRecordingService` class:
    *   Update the `initialize` method to accept a second optional parameter `kind` of type `'main' | 'subagent'`.
    *   Ensure that if `initialize` is called with `kind='subagent'`, the conversation record written to disk has `kind` set to `'subagent'`.

*   Adjust the `SessionSelector` class in `packages/cli/src/utils/sessionUtils.ts`:
    *   Modify the `listSessions` method to filter out sessions whose `ConversationRecord` has `kind === 'subagent'`.
    *   Ensure sessions with `kind === 'main'` or with no `kind` field are returned.

*   Enhance error handling in `LocalAgentExecutor` located in `packages/core/src/agents/local-executor.ts`:
    *   When failing to create a `GeminiChat` object, throw an error message: `'Failed to create chat object: '` concatenated with the result of `getErrorMessage()` applied to the caught error.
    *   When reporting a chat-creation failure via the activity callback, set the error field in the activity data to `'Error: Failed to create chat object: '` concatenated with the result of `getErrorMessage()` applied to the caught error.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.