Implement dynamic behavior and error handling for a CLI tool operating in "Plan Mode". Ensure agents are aware of their operational constraints and provide clear error messages when plan files are missing.

*   Update the `GeneralistAgent` function:
    *   Ensure the `description` property is a dynamic getter.
    *   When in Plan Mode, include 'large-scale investigation and batch planning' in the description.
    *   Exclude 'batch refactoring/error fixing' from the description in Plan Mode.
    *   For default mode, include 'batch refactoring/error fixing' and exclude 'large-scale investigation and batch planning'.
    *   Use `context.config.getApprovalMode()` to determine the current mode.
    *   Import `ApprovalMode` from `../policy/types.js`.

*   Modify the `LocalAgentExecutor` class:
    *   In the `create` method, check if `config.getApprovalMode() === ApprovalMode.PLAN`.
    *   If true, append a section with the heading `# Execution Constraints` to the system instruction.
    *   Include the text: 'You are currently operating in Plan Mode. Your write tools are globally restricted to only modifying plan (.md) files in the plans directory: <plansDir>/'.
    *   Replace `<plansDir>` with the value from `config.storage.getPlansDir()`.
    *   Import `ApprovalMode` from `../policy/types.js`.

*   Export validation functions from `@google/gemini-cli-core`:
    *   `validatePlanPath`: Ensure it returns a Promise resolving to a string error message or undefined.
    *   `validatePlanContent`: Ensure it returns a Promise resolving to a string error message or undefined.

*   Handle missing plan file errors:
    *   When rendering a plan confirmation dialog for a non-existent file, display 'File not found: /path/to/plan'.
    *   Ensure the rendering process allows for asynchronous state updates before assertions.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.