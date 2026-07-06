## Description

The Button component is missing dark mode support for its text color in the default (primary) variant. When the user's device is set to dark mode, the button's background changes to a light color, but the label text stays white — making it invisible or very hard to read. The button label needs to adapt its text color for dark mode so that it remains legible in both light and dark themes.

## Expected Behavior

- In light mode, the default button label text should continue to appear white.
- In dark mode, the default button label text should switch to black to contrast with the lighter button background.
- When the button is rendered at large size, the label's applied style classes should include both the light mode text color and the dark mode text color override.

## Why This Matters

Without this fix, users who have dark mode enabled on their devices will see white text on a white (or near-white) button background, rendering the button label completely unreadable. This is a straightforward accessibility and usability issue that affects all users in dark mode.
