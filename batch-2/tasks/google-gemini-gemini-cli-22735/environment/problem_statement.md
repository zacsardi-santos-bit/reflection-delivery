## Description

The todo list tool currently supports four task statuses: pending, in progress, completed, and cancelled. However, there is no way to mark a task as **blocked** — meaning a task that cannot currently be worked on due to an external dependency, missing information, or other impediment.

This gap means that when an agent encounters a situation where it cannot proceed on a specific subtask (perhaps because it's waiting on another task, or because something external needs to happen first), there is no way to accurately represent that state in the todo list.

## Expected Behavior

- A new "blocked" status should be supported for todo items
- Blocked tasks should be visually distinct in the UI
- Blocked tasks should appear in the task list between pending tasks and completed tasks, so users can quickly see what work is stalled
- The task tracking system should recognize blocked tasks as a separate state from both pending and cancelled tasks

## Why This Matters

Without a blocked status, agents are forced to either leave tasks as pending (which implies they could be worked on but haven't been started) or cancel them entirely (which wrongly implies the task won't be done). A blocked status accurately communicates that the task exists and should eventually be completed, but cannot be actioned right now.
