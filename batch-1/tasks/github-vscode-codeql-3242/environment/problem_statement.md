## Description

The suggest box component in the extension's UI does not respond to keyboard shortcuts that would let users manually trigger the suggestion dropdown. Specifically, users should be able to press a well-known "trigger suggestions" key combination to open the dropdown when it is closed, without having to click with the mouse. Currently there is no keyboard-driven way to open the popup once it has been dismissed.

Additionally, a utility for creating stable callback references — where the function identity stays the same across re-renders even as its internal logic is updated — is needed to support the suggestion trigger handler without causing unnecessary re-renders. This utility does not yet exist as a standalone, reusable hook.

Finally, a shared mock object helper used in tests exists only in one location inside the VS Code test utilities. It should be extracted to a shared top-level location so it can be reused from both view-layer tests and VS Code integration tests.

## Expected Behavior

- Users pressing the "trigger suggestions" shortcut (Ctrl+Space) while the suggest input is focused should cause the dropdown to open, if it is not already open.
- Pressing the shortcut when the dropdown is already open should have no effect.
- Only the exact key combination should trigger the open action; any variant with additional modifier keys (Shift, Alt, Meta/Cmd) or a different key should be ignored.
- When the shortcut triggers the open action, the default browser/OS behavior for that key combination should be suppressed.
- The suggestion trigger handler's function reference should remain stable (same identity) across re-renders even when surrounding context changes, to avoid causing performance issues through unnecessary re-renders.
- The shared mock object utility and its associated types should be importable from a common top-level test location.

## Why This Matters

Keyboard-only users and power users who prefer keyboard navigation need to be able to trigger the suggestion dropdown without using the mouse. Without this shortcut, the suggest box is harder to use efficiently. Providing a stable callback reference for the handler ensures there are no unintended side effects or performance regressions from re-renders.
