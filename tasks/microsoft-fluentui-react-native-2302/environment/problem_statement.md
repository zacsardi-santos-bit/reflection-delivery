## Description

The Android theme is missing several foreground color tokens that components need when rendering disabled elements or elements displayed on colored/filled backgrounds. Without these tokens, any component that tries to look up the appropriate foreground color for a disabled state or an "on-color" context will find nothing in the theme — leaving it unable to style itself correctly.

## Expected Behavior

- The Android theme should expose a token for the primary disabled foreground color, with an appropriate value for both light and dark appearances.
- The Android theme should expose a token for a secondary disabled foreground color, also adapted to light and dark appearances.
- The Android theme should expose a token for the foreground color used when content appears on a colored/filled background, again adapted to light and dark appearances.

## Why This Matters

Components like tabs or other interactive controls need to show visually distinct states for disabled items. If the theme does not supply the right color tokens, those components either fall back to incorrect values or fail to render disabled states correctly. Adding these missing tokens ensures that all appearance modes (light, dark, high contrast, dynamic) produce the right colors and that the theme is consistent with design specifications.
