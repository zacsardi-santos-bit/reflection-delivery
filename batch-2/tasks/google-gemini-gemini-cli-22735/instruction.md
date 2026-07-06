Implement a "blocked" status for the todo list management feature to accurately represent tasks that cannot currently be worked on. Ensure this new status is fully integrated into the system, including UI representation and task sorting.

*   Update the `TodoStatus` type in `packages/core/src/tools/tools.ts` to include 'blocked'.
*   Modify the `WriteTodosTool` class in `packages/core/src/tools/write-todos.ts`:
    *   Ensure the TODO_STATUSES constant includes 'blocked' for validation.
    *   Update the `buildAndExecute` method to format blocked items in `llmContent` as 'N. [blocked] <description>'.
*   Update the `TaskStatus` enum in `packages/core/src/services/trackerTypes.ts` to include `BLOCKED = 'blocked'`.
*   Modify the `buildTodosReturnDisplay` function in `packages/core/src/tools/trackerTools.ts`:
    *   Map `TaskStatus.BLOCKED` to a display status of 'blocked'.
    *   Sort blocked tasks at position 2, after in_progress (0) and open (1), but before closed (3).
*   Update the `ChecklistStatus` type in `packages/cli/src/ui/components/ChecklistItem.tsx` to include 'blocked'.
*   Modify the `ChecklistItem` component in `packages/cli/src/ui/components/ChecklistItem.tsx`:
    *   Render blocked items with the ⛔ symbol followed by the item label (e.g., "⛔ Blocked this").

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.