## Description

The Pulumi CLI should support managing scheduled deployment actions for stacks. Currently, there is no way to create, list, view, edit, or remove scheduled operations on a stack from the command line. Users who want to automate recurring operations — such as periodically checking for infrastructure drift, destroying stacks on a time-to-live basis, or running arbitrary deployment commands on a schedule — have no CLI-native tooling to manage these.

## Expected Behavior

- Users can create a new scheduled action for a stack by specifying the schedule type (raw deployment, drift detection, or TTL destroy), a cron expression or one-time timestamp, and any type-specific options.
- Users can list all schedules for a stack, with output in both table and JSON formats, and can limit the number of results shown.
- Users can view the details of a specific schedule by ID, with both text and JSON output.
- Users can edit an existing schedule by changing only the fields they want to update (unchanged fields are preserved automatically).
- Users can delete a scheduled action by ID.
- The CLI validates that flags are compatible with the schedule type and returns informative errors for invalid combinations (for example, a cron expression cannot be used for a TTL schedule).
- When a schedule is removed, a confirmation message is displayed.
- When no edit flags are provided to the edit command, an error is returned asking the user to supply at least one change.

## Why This Matters

This feature closes a gap in the Pulumi CLI's ability to manage the full lifecycle of a stack. Without it, users must rely on external tooling or the web UI to manage scheduled automation, making it harder to script and automate their infrastructure workflows.
