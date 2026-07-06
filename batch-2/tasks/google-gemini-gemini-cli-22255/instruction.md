Implement the necessary changes to address the issues in the policy update mechanism for the tool confirmation flow in the AI agent framework. Ensure the correct tool name pattern is used for MCP server tools and refactor the function to explicitly accept the message bus as a parameter.

*   Update the `updatePolicy` function in `packages/core/src/scheduler/policy.ts`:
    *   Modify the function signature to accept the message bus as an explicit 4th parameter.
    *   Ensure the `messageBus` is no longer extracted from the `AgentLoopContext` object.

*   Adjust all callers of the `updatePolicy` function:
    *   Pass the message bus as the 4th argument.
    *   Ensure the `messageBus` is not read from the `AgentLoopContext` object.

*   Implement the correct tool name pattern for MCP server tools:
    *   When processing a `ProceedAlwaysServer` outcome, set the `toolName` in the `UPDATE_POLICY` message to follow the format `mcp_{serverName}_*`.
    *   Example: For a server named 'my-server', the `toolName` should be `mcp_my-server_*`.

*   Ensure the message bus publish method is called only for specific outcomes:
    *   Do not call the publish method for outcomes `ProceedOnce`, `Cancel`, or `ModifyWithEditor`.

*   Update the scheduler's call to `updatePolicy`:
    *   Pass the message bus as the 4th positional argument after `outcome`, `details`, and `config`.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.