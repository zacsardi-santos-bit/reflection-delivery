I'm working on a task management app and I'd like to add subtask support. Right now tasks are completely flat, but I want to be able to nest tasks under a parent task so users can break down larger pieces of work into smaller steps.

Here's what I need:

Users should be able to create a task with a list of sub-items in a single request, and also add, edit, or remove sub-items when updating an existing task (empty sub-item names should be ignored). There should also be a dedicated endpoint to fetch the sub-items of a given task, returning an empty list when there are none or when the task doesn't exist. Unauthenticated requests to that endpoint should be rejected, and requests for another user's task's sub-items should return an empty list rather than an error.

The completion behavior is the important part: when a parent task is marked done, all its sub-items should automatically be marked done too (with their completion timestamp set). When a parent task is marked undone, all sub-items should revert to incomplete (timestamps cleared). In the other direction, when a sub-item is marked done and it was the last incomplete one, the parent should automatically become done. When any sub-item is marked undone, the parent should immediately revert to incomplete. Toggling a standalone task with no sub-items should still work normally.

The main task list endpoint should only return top-level tasks — sub-items should not appear at the top level. The "completed today" metrics data should similarly exclude sub-items. Trying to create a sub-item under a task belonging to another user should return an error.

Finally, there's a related issue with recurring tasks: right now deleting a recurring task template is blocked by a database constraint. I'd like that behavior changed so the deletion succeeds and the child recurring instances simply have their reference to the deleted template cleared, rather than the whole operation being rejected.
