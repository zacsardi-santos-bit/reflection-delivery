Implement the following improvements in the agent integration layer to enhance message retrieval and registry management.

*   Update the `A2AResultReassembler` class:
    *   Implement a method `toActivityItems()` in `packages/core/src/agents/a2aUtils.ts`.
    *   Ensure `toActivityItems()` returns an array of `SubagentActivityItem` objects.
    *   Each `SubagentActivityItem` must include:
        *   An 'id' field formatted as 'msg-{index}' with a 0-based index.
        *   A 'type' field set to 'thought'.
        *   A 'content' field containing the text from the message parts.
        *   A 'status' field set to 'completed'.
    *   Ensure `toActivityItems()` returns one activity item per message received via `update()`, in the order they were received.

*   Modify the `Config` class:
    *   Implement a public method `getAgentRegistry()` in `packages/core/src/config/config.ts`.
    *   Ensure `getAgentRegistry()` returns the `AgentRegistry` instance, allowing external callers to access the registry.

*   Update the `AgentRegistry` class:
    *   Implement a public async method `reload()` in the appropriate file (likely `packages/core/src/agents/` or `packages/core/src/config/`).
    *   Ensure `reload()` re-loads and re-registers all agent definitions.
    *   The `reload()` method must update the registry state so that enabled agents appear in `getAllDefinitions()` and disabled agents do not.
    *   Ensure `reload()` provides the same functionality as the private `Config.onAgentsRefreshed()` method.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.