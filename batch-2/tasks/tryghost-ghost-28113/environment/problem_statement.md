## Description

When a user edits an automation that is already active (live) and clicks the publish button to save their changes, the update fires immediately without any confirmation. This is risky because active automations may currently be running for existing members — publishing changes without warning could affect both future runs and any in-progress ones in unexpected ways.

## Expected Behavior

- Clicking "Publish changes" on an **active** automation with unsaved local edits should **not** immediately save. Instead, it should open a confirmation dialog.
- The dialog should clearly explain that the update will affect new runs of the automation as well as actively-running ones, so the user understands the consequences before confirming.
- If the user confirms, the save should proceed. While saving is in progress, the confirm button inside the dialog should show a loading state and be disabled.
- If the save fails, the dialog should stay open and present a retry option styled to indicate an error state, so the user can try again without losing their context.

## Why This Matters

Active automations are live workflows that may already be executing for real members. Accidentally publishing half-finished edits to a live automation can break member experiences. A confirmation step with clear impact messaging gives users a chance to reconsider and provides graceful handling of failures.
