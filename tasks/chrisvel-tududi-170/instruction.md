Implement subtask support in the task management app to allow users to break down larger tasks into smaller, manageable steps. Ensure that completion states between parent tasks and subtasks are synchronized automatically. Update the system to handle recurring tasks appropriately when their templates are deleted.

*   Update the Task model:
    *   Add a nullable `parent_task_id` field as a self-referencing foreign key to Task.id.
    *   Define a 'ParentTask' belongs-to self-association using `parent_task_id` and a 'Subtasks' has-many self-association using the same foreign key.

*   Implement the GET endpoint `/api/task/:id/subtasks`:
    *   Return HTTP 200 with a JSON array of subtask objects for the given task id.
    *   Return an empty array for tasks with no subtasks or non-existent task IDs.
    *   Return HTTP 401 for unauthenticated requests.
    *   Return HTTP 200 with an empty array for requests from an authenticated user for another user's task.

*   Implement the POST endpoint `/api/task` to handle subtasks:
    *   Accept an optional 'subtasks' array in the request body.
    *   Create subtasks with non-empty names, setting `parent_task_id` to the new task's id.
    *   Ignore entries with empty or whitespace-only names.
    *   Return HTTP 400 if `parent_task_id` references a task belonging to a different user.

*   Implement the PATCH endpoint `/api/task/:id` for updating tasks with subtasks:
    *   Accept an optional 'subtasks' array.
    *   Create new subtasks for entries with `isNew: true`.
    *   Update the name of existing subtasks with `id` and `isEdited: true`.
    *   Delete existing subtasks whose id does not appear in the array.
    *   Delete all subtasks if an empty array is provided.

*   Implement the PATCH endpoint `/api/task/:id/toggle_completion`:
    *   Toggle parent task completion, updating all subtasks' status and `completed_at` accordingly.
    *   When a subtask is marked done and all siblings are done, set the parent task to done.
    *   When any subtask is marked incomplete, revert the parent task to incomplete.

*   Update the GET endpoint `/api/tasks`:
    *   Return only top-level tasks in the 'tasks' array and metrics.tasks_completed_today array.
    *   Exclude subtasks from appearing at the top level.

*   Update the DELETE endpoint `/api/task/:id`:
    *   Ensure successful deletion of a task.
    *   For recurring tasks, set `recurring_parent_id` to null for child tasks upon parent deletion.

*   Modify the database constraint for recurring tasks:
    *   Use ON DELETE SET NULL for `recurring_parent_id` to allow deletion of recurring parent tasks without blocking due to child tasks.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.