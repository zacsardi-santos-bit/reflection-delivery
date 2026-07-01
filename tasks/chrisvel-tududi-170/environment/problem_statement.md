## Description

The app currently only supports flat, standalone tasks. There's no way to break a large task into smaller, trackable sub-items or to group related work under a single parent task. This makes it hard to manage complex work that naturally decomposes into steps.

We need to add subtask support so that any task can have child tasks linked to it. Completion state should stay in sync automatically: completing the parent should complete all sub-items, and completing the last sub-item should auto-complete the parent. Unchecking the parent should revert all sub-items, and unchecking any sub-item should revert the parent.

## Expected Behavior

- Tasks can be created with a list of sub-items at the same time, or sub-items can be added/edited/removed when updating an existing task.
- An endpoint is available to retrieve the sub-items of any given task.
- When a task is marked complete, all its sub-items are also marked complete. When it is marked incomplete, all sub-items revert to incomplete.
- When a sub-item is marked complete and it is the last remaining incomplete sub-item, the parent task is automatically marked complete.
- When any sub-item is marked incomplete, the parent task automatically reverts to incomplete.
- The main task list only shows top-level tasks. Sub-items do not appear at the top level of the list — they should be accessible through their parent task.
- The "completed today" metrics view also only shows top-level tasks, not sub-items.
- Attempting to create a sub-item under another user's task is rejected.
- Unauthenticated access to sub-item data is rejected.

## Related Change

The way recurring task series behave when their template is deleted should also be updated: child instances should survive the deletion (with their connection to the deleted template cleared out) rather than the deletion being prevented by a database constraint.

## Why This Matters

Without subtask support, users have to track complex, multi-step work as a single opaque item or manage a cluttered flat list of loosely-related items with no clear hierarchy. Subtasks make it easy to see progress within a larger piece of work and keep the main task list focused on high-level goals.
