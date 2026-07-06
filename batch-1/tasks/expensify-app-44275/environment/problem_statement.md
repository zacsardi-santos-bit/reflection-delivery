## Description

Workspace administrators need the ability to create custom report fields that appear on expense reports. These fields come in three varieties: free text input, a date picker, and a dropdown/list of options. Currently, there is no action layer to programmatically create these fields, so the workspace settings flow for adding report fields cannot function.

## Expected Behavior

- When an admin creates a text-type report field, the field is immediately added to the workspace with the specified name, type, and default value — with no pre-populated dropdown options.
- When an admin creates a date-type report field, the field is added with the specified name, type, and a date as the default value.
- When an admin creates a list/dropdown-type report field, the field is added with the values and their enabled/disabled states that were configured during field setup.
- In all cases, the field should appear optimistically in the workspace immediately after creation, and the pending state should be cleared once the server confirms the change.

## Why This Matters

Without this functionality, workspace admins cannot create custom report fields through the settings UI. This blocks the ability to prompt report submitters for extra structured information (like free text, dates, or predefined choices) on their expense reports.
