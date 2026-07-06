Implement the ability to surface intermediate status messages from a remote agent as distinct activity entries and expose a public interface for reloading the agent registry. Ensure the reload process correctly handles agent registration without double-initialization.

*   Update the `A2AResultReassembler` class in `packages/core/src/agents/a2aUtils.ts`:
    *   Modify the `toActivityItems()` method to:
        *   Return one activity item per message in the internal message log.
        *   Ensure each activity item has the shape:
            *   `id`: 'msg-${index}' (e.g., 'msg-0', 'msg-1'), where index is the 0-based position of the message.
            *   `type`: 'thought'.
            *   `content`: Trimmed text extracted from the message parts.
            *   `status`: 'completed'.
        *   Exclude the generic 'pending/Working...' placeholder item when the message log is non-empty and contains no auth-required entry.

*   Update the `Config` class in `packages/core/src/config/config.ts`:
    *   Implement a public `getAgentRegistry()` method that returns the agent registry object.
    *   Ensure the returned agent registry object exposes a public `reload()` method:
        *   Signature: `reload() -> Promise<void>`.
        *   Correctly re-register agents that were previously disabled and then re-enabled.
        *   Avoid double-initialization of the registry.
        *   Unregister agents that have been disabled since the last load.
        *   Register agents that have been enabled since the last load.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.