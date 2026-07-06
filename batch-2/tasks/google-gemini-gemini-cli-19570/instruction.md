Implement the necessary logic to address inconsistencies in the fully-automatic approval mode of an AI assistant. Ensure that system instructions are refreshed appropriately and that redundant tools are not registered when this mode is active.

*   Update the `setApprovalMode` method in `packages/core/src/config/config.ts`:
    *   When `setApprovalMode` is called with `ApprovalMode.YOLO`, invoke `updateSystemInstructionIfInitialized`.
    *   Ensure `updateSystemInstructionIfInitialized` is not called when `setApprovalMode` is invoked with modes other than `ApprovalMode.PLAN` or `ApprovalMode.YOLO`.

*   Modify the `syncPlanModeTools` method in `packages/core/src/config/config.ts`:
    *   When the current approval mode is `ApprovalMode.YOLO`, ensure that `EnterPlanModeTool` is not registered in the tool registry, even if the plan feature is enabled in the configuration.

*   Ensure the `EnterPlanModeTool` class in `packages/core/src/tools/enter-plan-mode.ts` is not registered by `syncPlanModeTools` when the approval mode is `YOLO`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.