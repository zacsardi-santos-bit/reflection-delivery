## Description

The automations admin API currently supports browsing and reading automations, but there is no way to change an automation's status (active/inactive) through the API. Administrators need to be able to enable or disable automations programmatically without resorting to direct database access.

## Expected Behavior

- The admin API should expose an update operation for automations that allows changing the status of an existing automation.
- The update operation should only affect the automation's status — attempting to change the name, steps, or connections alongside the status should be silently ignored; those fields remain unchanged.
- A successful update should return the full automation record, confirming the new status while reflecting the unchanged name, steps, and connections.
- If an invalid status value is supplied, the API should reject the request with a clear validation error that names the acceptable values and includes the problematic value that was provided.
- If the status field is omitted entirely, the API should also reject the request with a validation error listing the acceptable values.

## Why This Matters

Without this endpoint, the only way to activate or deactivate an automation is through direct database manipulation. Adding an API-level edit operation allows admin clients and integrations to toggle automation status safely and with proper validation feedback.
