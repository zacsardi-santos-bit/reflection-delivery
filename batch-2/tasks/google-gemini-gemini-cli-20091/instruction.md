Upgrade the remote agent communication system to support streaming responses. Implement a streaming method on the agent client manager that returns an async iterable of results. Develop utility functions for task state detection, response ID extraction, and reassembling streaming events into a coherent output.

*   Implement the `sendMessageStream` method in the `A2AClientManager` class located at `packages/core/src/agents/a2a-client-manager.ts`.
    *   Accept parameters: `agentName` (string), `message` (string), and an optional `options` object with `contextId`, `taskId`, and `signal`.
    *   Return an async iterable of `SendMessageResult` values.
    *   Embed `contextId` and `taskId` in the message payload if provided.
    *   Throw an error with "Agent '<name>' not found." if the agent is not loaded.
    *   Re-throw errors from the underlying client's `sendMessageStream` with a prefixed message.

*   Ensure `SendMessageResult` type is exported from `a2a-client-manager`.

*   Create the `isTerminalState` function in `a2aUtils` at `packages/core/src/agents/a2aUtils.ts`.
    *   Accept a `state` string or undefined.
    *   Return `true` for terminal states: 'completed', 'failed', 'canceled', 'rejected'.
    *   Return `false` for non-terminal states and undefined.

*   Develop the `extractIdsFromResponse` function in `a2aUtils`.
    *   Return an object with `contextId`, `taskId`, and `clearTaskId`.
    *   Set `clearTaskId` to `true` for terminal state responses and `false` otherwise.

*   Implement the `A2AResultReassembler` class in `a2aUtils`.
    *   Provide an `update(result)` method to process streaming events.
    *   Handle status-update, artifact-update, and message events appropriately.
    *   Implement `toString()` to return the reassembled text, joining sections with '\n\n'.

*   Update `execute` method in `RemoteAgentInvocation` at `packages/core/src/agents/remote-invocation.ts`.
    *   Accept `signal` (AbortSignal) and optional `updateOutput` callback.
    *   Use `sendMessageStream` with the signal in options.
    *   Call `updateOutput` with reassembled text after each chunk.
    *   Resolve with an error message 'Operation aborted' if the abort signal fires.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.