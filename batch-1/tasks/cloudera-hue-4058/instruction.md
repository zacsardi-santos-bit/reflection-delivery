Implement a task server management UI in the admin section of your application using React components. Create components for displaying a list of server tasks, scheduling new tasks, and viewing task logs. Ensure the interface allows administrators to search, filter, schedule, and manage tasks effectively.

*   Implement the `ScheduleTaskModal` component:
    *   Accept an `onClose` callback prop.
    *   Render a radio group with two options: 'Document Cleanup' and 'Tmp Cleanup'.
    *   When 'Document Cleanup' is selected, show one numeric input with `name='keep_days'`.
    *   When 'Tmp Cleanup' is selected, show two numeric inputs with appropriate `name` attributes.
    *   Include a 'Close' button that triggers `onClose`.
    *   On form submission, invoke `useSaveData` with `SCHEDULE_NEW_TASK_URL` and submit the task with appropriate parameters.

*   Implement the `TaskLogsModal` component:
    *   Accept `taskId` and `onClose` as props.
    *   Display a heading 'Task Logs' and fetch log data using `taskId`.
    *   Include a 'Close' button that triggers `onClose`.

*   Implement the `TaskServer` component:
    *   Fetch and display a list of tasks using `useLoadData`.
    *   Render each task's `taskId` visibly.
    *   Include a search input with placeholder 'Search by task name, user ID, or task ID...'.
    *   Provide status filter checkboxes, including 'Succeeded'.
    *   Include a 'Schedule Task' button to open `ScheduleTaskModal`.
    *   Ensure `ScheduleTaskModal` has `data-testid='hue-schedule-task__modal'` and 'Schedule Task' text appears twice when open.
    *   Provide row-level checkboxes for task selection and a 'Kill Task' button.
    *   On 'Kill Task' click, invoke `useSaveData` with `KILL_TASK_URL` and submit selected task IDs.

*   Define constants and types:
    *   `SCHEDULE_NEW_TASK_URL` and `KILL_TASK_URL` in `constants.ts`.
    *   `scheduleTasksCategory` array with specified task categories and parameters.
    *   `TaskStatus` enum with `Success`, `Failure`, and `Running`.
    *   `TaskServerResponse` interface with specified fields and structure.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.