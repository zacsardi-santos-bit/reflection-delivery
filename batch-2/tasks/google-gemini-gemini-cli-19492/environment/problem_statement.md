## Description

When a user opens a project folder for the first time, the CLI displays a dialog asking whether to trust the folder. Currently this dialog shows only a generic question and brief explanation, with no information about what configurations or extensions the project actually contains. Users are making a trust decision completely blind — they cannot see whether the folder defines custom commands, connects to external servers, registers automation hooks, includes agent skills, or overrides global settings. If any of those settings are potentially dangerous (such as disabling the security sandbox or auto-approving tool executions), users have no way of knowing before they click "trust."

## Expected Behavior

- Before displaying the trust dialog, the CLI should scan the folder's configuration directory to discover what it contains.
- The trust dialog should display a summary of discovered items grouped by type: custom commands, server integrations, hooks, skills, and configuration overrides.
- If any discovered settings are potentially risky, the dialog should display clear security warnings so the user can make an informed decision.
- If scanning encounters errors (for example, a malformed configuration file), those errors should be surfaced in the dialog.
- All displayed content should be sanitized to remove terminal formatting codes that could corrupt the UI layout.
- The dialog should gracefully handle terminal size constraints, truncating content when necessary and allowing the user to expand it.

## Why This Matters

Users cannot meaningfully consent to trusting a folder without knowing what they are trusting. This change provides transparency about what a project folder would activate, reducing the risk that users unknowingly trust malicious or misconfigured projects.

## Additional Fix

The scrollable content component used in the CLI currently defaults to starting at the bottom of content rather than the top. This behavior should be corrected so that scrollable regions start at the top by default, with an explicit option to start at the bottom when appropriate.
