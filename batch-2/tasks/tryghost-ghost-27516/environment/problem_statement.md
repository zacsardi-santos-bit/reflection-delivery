## Description

When users open the email design customization modal in Ghost Admin and click the button color field to open the color picker, pressing the Escape key incorrectly dismisses the entire customization modal instead of just closing the color picker.

This happens in two scenarios:
- When the color picker is fully visible on screen and a user presses Escape to close it
- When a user presses Escape immediately after clicking the button color field, before the color picker has finished appearing on screen

In both cases, the expected behavior is that Escape should close only the color picker, leaving the customization modal open so the user can continue editing settings.

## Expected Behavior

- Pressing Escape while the color picker is open should close the color picker only
- The email customization modal should remain visible after the color picker is closed via Escape
- This should work even if Escape is pressed immediately after clicking the color picker trigger, before the popover has fully rendered

## Why This Matters

Users lose their unsaved changes when Escape accidentally closes the entire modal instead of just the nested color picker. This is especially disruptive because the user likely intended to cancel only the color picker interaction, not their entire customization session.
