Implement a mechanism to block user input in a browser during automated interactive operations by injecting an overlay. Ensure the overlay is removed after the operation, even if it fails, and provide a flag to enable or disable this feature.

*   Implement the `injectInputBlocker` function in `packages/core/src/agents/browser/inputBlocker.ts`.
    *   Call `browserManager.callTool` with the tool name 'evaluate_script' and an argument object using the key 'function'.
    *   Use an arrow function script string that references the DOM element identifier `__gemini_input_blocker`.
    *   Include the text 'Gemini CLI is controlling this browser' and set 'aria-hidden' on the blocker element.
    *   Catch errors from `callTool` and resolve with `undefined`.

*   Implement the `removeInputBlocker` function in `packages/core/src/agents/browser/inputBlocker.ts`.
    *   Call `browserManager.callTool` with the tool name 'evaluate_script' and an argument object using the key 'function'.
    *   Reference the identifier `__gemini_input_blocker` in the script string.
    *   Catch errors from `callTool` and resolve with `undefined`.

*   Update `createMcpDeclarativeTools` in `packages/core/src/agents/browser/mcpToolWrapper.ts`.
    *   Accept a third boolean parameter `shouldDisableInput` (default false).
    *   When `shouldDisableInput` is true and an interactive tool is executed:
        *   Invoke `callTool` three times: 
            1. `evaluate_script` to suspend the blocker.
            2. The actual tool call (e.g., 'click').
            3. `evaluate_script` to resume the blocker.
    *   For read-only tools like 'take_snapshot', make only the actual tool call.
    *   When `shouldDisableInput` is false, make only the actual tool call for any tool.
    *   If an interactive tool fails with `shouldDisableInput=true`, still make the resume `evaluate_script` call and return an error property in the result.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.