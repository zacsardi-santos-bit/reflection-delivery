## Description

There are two bugs in the DAG run "clear" operation that affect dry-run mode in the Airflow web UI and API:

**Bug 1: Dry-run response is missing task instance fields, causing empty UI modal**

When a user triggers a dry-run of the "clear DAG run" action from the UI, the confirmation modal should display a list of task instances that would be affected. Instead, the list appears empty. The API is returning task instance objects, but without the necessary relationship data needed to populate fields like the DAG's display name. Because those fields require database joins that are not being performed, the serialization silently fails and the UI receives no usable data.

**Bug 2: "Only new tasks" feature does not correctly identify new tasks**

The option to clear only tasks that are "new" to a DAG run (i.e., tasks added to the DAG definition after the run was created) uses a DAG version comparison strategy that does not work reliably in all DAG bundle configurations. In some setups, this approach returns an empty set even when there are genuinely new tasks, or returns incorrect results.

## Expected Behavior

- A dry-run clear request must return fully populated task instance data (display names, run IDs, states, and all other response fields).
- When filtering to only failed tasks, only task instances with a failed state should be included, each with complete field data.
- The "only new tasks" detection must use a task-instance existence check: a task is "new" if it appears in the latest DAG version but has no existing task instance for the current run. This approach is reliable across all DAG bundle configurations.
- After new task instances are created via a real (non-dry-run) "only new" clear, a subsequent dry-run preview should show zero new tasks.

## Why This Matters

The dry-run confirmation modal is a key safety check that lets users see what will be re-run before committing. If it always appears empty, users cannot make informed decisions. The "only new tasks" bug means users trying to queue newly added tasks in an existing run get unpredictable results depending on how their DAGs are bundled.
