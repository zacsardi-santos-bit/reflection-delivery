## Description

When running the CLI execute command without providing an action ID and without requesting help, the command silently displays help output without any error message. This is misleading because the user gets the same help display they would see when explicitly requesting help, with no indication that they did something wrong.

## Expected Behavior

- When the execute command is invoked without an action ID and without the help flag, it should display the help text and then throw a clear error indicating that the action ID is required.
- When the execute command is invoked with the help flag but no action ID, the current behavior (showing help without an error) should remain unchanged.
- Auth resolution should not be attempted in either help scenario.

## Why This Matters

Users who accidentally omit the required action ID receive no feedback about what went wrong — they just see the help text and don't know they need to provide an action ID. Adding an explicit error thrown after showing help makes the interface clearer and helps developers quickly understand what they need to fix.
