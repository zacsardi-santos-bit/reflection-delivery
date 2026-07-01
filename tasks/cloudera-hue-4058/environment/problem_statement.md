## Description

The admin area needs a proper task server management interface to replace the old task browser. Administrators need to be able to see all background tasks currently running or recently completed on the server, search and filter them, schedule new maintenance tasks, kill unwanted tasks, and inspect task logs — all through a unified UI.

## Expected Behavior

- A task list view that shows each task's unique identifier, and updates automatically as tasks change state.
- A search field (with the prompt "Search by task name, user ID, or task ID...") that narrows the list in real time based on task name, user, or task identifier.
- Status filter checkboxes ("Succeeded", "Running", "Failed") so administrators can quickly isolate tasks by outcome.
- A "Schedule Task" button that opens a modal allowing the user to pick a task category from a predefined list (including a document cleanup task and a temporary-file cleanup task), fill in the required numeric parameters, and submit the job.
- A "Kill Task" button that becomes actionable after selecting one or more tasks via checkboxes; it should send the selected task identifiers to the backend to be terminated.
- A separate task logs modal that displays the raw log output for a chosen task.

## Why This Matters

Without this interface, administrators have no convenient way to monitor or control long-running background maintenance jobs. The previous implementation lacked the correct task category values and parameter names needed by the backend, and was not organized as maintainable separate components. This work restructures everything into a clean, testable set of components with the correct identifiers aligned to the backend API.
