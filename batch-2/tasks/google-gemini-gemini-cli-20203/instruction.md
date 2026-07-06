Implement functionality to notify the AI model when a user exits planning mode, either manually or automatically, by updating the conversation history with a contextual message. Develop utilities to generate human-readable descriptions for each approval mode and compose appropriate exit messages.

*   Implement `getApprovalModeDescription` in `packages/core/src/utils/approvalModeUtils.ts`:
    *   Accept an `ApprovalMode` enum value.
    *   Return a string description:
        *   `ApprovalMode.DEFAULT`: 'Default mode (edits will require confirmation)'
        *   `ApprovalMode.AUTO_EDIT`: 'Auto-Edit mode (edits will be applied automatically)'
        *   `ApprovalMode.PLAN`: 'Plan mode (read-only planning)'
        *   `ApprovalMode.YOLO`: 'YOLO mode (all tool calls auto-approved)'

*   Implement `getPlanModeExitMessage` in `packages/core/src/utils/approvalModeUtils.ts`:
    *   Accept an `ApprovalMode` enum value and an optional `boolean` parameter `manual`.
    *   Default `manual` to `false`.
    *   Return a string:
        *   If `manual` is `true`: 'User has manually exited Plan Mode. Switching to <description>.'
        *   If `manual` is `false` or omitted: 'Plan approved. Switching to <description>.'
    *   Use `getApprovalModeDescription` to generate `<description>`.

*   Export both `getApprovalModeDescription` and `getPlanModeExitMessage` from `packages/core/src/utils/approvalModeUtils.ts`.

*   Re-export `getPlanModeExitMessage` from the public barrel file of the `@google/gemini-cli-core` package to allow CLI-layer access.

*   Update `handleApprovalModeChange` to inject a notification message when manually switching from Plan Mode:
    *   Call `client.addHistory` with `role` as 'user' and `parts` containing a single text entry from `getPlanModeExitMessage(newMode, true)`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.