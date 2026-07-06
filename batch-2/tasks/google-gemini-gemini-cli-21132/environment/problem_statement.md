## Description

When the browser agent is actively controlling a browser on a user's behalf, there is currently no safeguard to prevent the user from accidentally interfering — clicking buttons, typing into forms, or otherwise disrupting the automation mid-session. This can cause unpredictable behavior and corrupt the agent's work.

We need a mechanism to temporarily block user input to the browser while the agent is performing interactive actions, then release that block once the action completes. This should include a visible banner informing the user that automation is in control, and the banner should not pollute accessibility tooling.

## Expected Behavior

- A new module handles injecting and removing an input-blocking overlay into the browser page.
- The overlay includes a visible message indicating that the automation agent is in control.
- The overlay must be transparent to accessibility tools (hidden from the accessibility tree).
- Input blocking is only applied before interactive (state-changing) operations, not before read-only ones like taking a screenshot.
- The blocking wrapper in the tool execution pipeline should accept a flag to enable or disable the feature.
- Even if an automated action fails, the input blocker must always be removed so the user is never locked out.
- Failures when injecting or removing the blocker must not crash the agent or interrupt the tool execution flow.

## Why This Matters

Without this, simultaneous user and agent interaction with the browser can cause race conditions or unexpected state changes. This protects the reliability of browser automation sessions and gives users a clear, accessible signal that the agent is working.
