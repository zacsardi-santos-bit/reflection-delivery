Update the browser agent's action limit logic to exclude internal operations from the user-configured action budget. Ensure only user-initiated browser interactions are counted towards the limit.

*   Modify the `callTool` method in `packages/core/src/agents/browser/browserManager.ts`:
    *   Add a fourth optional boolean parameter `isInternal` with a default value of `false`.
    *   When `isInternal` is `true`, do not increment the action counter and skip the maximum action limit check.
    *   When `isInternal` is `false` or omitted, check if the action counter is greater than or equal to `maxActionsPerTask` before incrementing.
        *   If the limit is reached, throw an error with the message 'maximum action limit (<N>)', where `<N>` is the configured limit.

*   Ensure the following functions call `callTool` with `true` as the fourth argument:
    *   `injectInputBlocker` in `packages/core/src/agents/browser/inputBlocker.ts` when invoking `evaluate_script`.
    *   `removeInputBlocker` in `packages/core/src/agents/browser/inputBlocker.ts` when invoking `evaluate_script`.
    *   `suspendInputBlocker` in `packages/core/src/agents/browser/inputBlocker.ts` when invoking `evaluate_script`.
    *   `resumeInputBlocker` in `packages/core/src/agents/browser/inputBlocker.ts` when invoking `evaluate_script`.

*   In the MCP tool wrapper, ensure input blocker suspend and resume operations around interactive tool calls pass `true` as the fourth argument to `callTool`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.