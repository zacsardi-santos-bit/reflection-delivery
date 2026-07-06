## Description

When a user opens a side conversation branching off from the current session, the side conversation's AI configuration does not reflect the runtime settings the user has configured during the session. Instead, the side fork falls back to persisted defaults, ignoring any model choice, reasoning intensity, resource tier, permission profile, approval policy, or approvals reviewer that the user has already set up in the parent thread.

## Expected Behavior

- When a side conversation is created, it should inherit all the currently-active runtime settings from the parent thread (model, reasoning intensity, resource tier, approval settings, permission profile, approvals reviewer) rather than using stored defaults.
- The developer guidance injected into side conversations should be set fresh and should not depend on any existing developer instructions in the parent configuration.

## Why This Matters

Users often adjust their AI settings during a session (switching models, tweaking reasoning modes, changing permission levels). When they open a side conversation, they expect it to behave consistently with the parent thread they've configured — not revert to whatever defaults were saved at startup. This inconsistency is confusing and can cause side conversations to behave in unintended ways.
