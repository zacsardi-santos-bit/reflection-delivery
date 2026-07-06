Implement a system to manage tool approval modes based on the current operating mode's permissiveness. Ensure that approvals granted in one mode apply only to that mode and more permissive modes, except when granted in the most restricted mode, which should apply globally. Update existing policy rules instead of creating duplicates.

*   Update the Config class:
    *   Implement the `getApprovalMode()` method in `packages/core/src/config/config.ts` to return the current `ApprovalMode`.

*   Modify the UpdatePolicy interface:
    *   Add an optional `modes` field of type `ApprovalMode[]` in `packages/core/src/confirmation-bus/types.ts`.

*   Define mode hierarchy:
    *   Create a constant `MODES_BY_PERMISSIVENESS` in `packages/core/src/policy/types.ts` as `[ApprovalMode.PLAN, ApprovalMode.DEFAULT, ApprovalMode.AUTO_EDIT, ApprovalMode.YOLO]`.

*   Update `updatePolicy` function:
    *   Implement in `packages/core/src/scheduler/policy.ts` to handle `ProceedAlwaysAndSave` outcomes.
    *   Use `context.config.getApprovalMode()` to determine the current mode.
    *   Compute applicable modes by slicing `MODES_BY_PERMISSIVENESS` from the current mode's index.
    *   Include the computed modes in the `UPDATE_POLICY` message to the message bus.

*   Implement `createPolicyUpdater` function:
    *   Set up in `packages/core/src/policy/config.ts` to listen for `UPDATE_POLICY` messages.
    *   Write the `modes` array to a TOML file when `persist` is true, using the format: `modes = [ "default", "yolo" ]`.
    *   Update existing rules in-place when a matching toolName (and optional mcpName, commandPrefix, argsPattern) exists, ensuring only one rule per tool.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.