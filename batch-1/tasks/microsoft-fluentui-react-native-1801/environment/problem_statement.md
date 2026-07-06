## Description

The Avatar component fails to display its fallback person icon in High Contrast mode. When a user's Avatar has no photo and no initials are available, the component is supposed to show a person silhouette icon as a fallback. However, in High Contrast themes this icon is completely invisible.

## Root Cause

The fallback icon is currently rendered through the generic icon slot, which introduces an intermediate wrapper view around the actual SVG element. This wrapper breaks the SVG's ability to inherit foreground colors from the theme — a mechanism that High Contrast mode depends on to colorize icons.

## Expected Behavior

- The Avatar component should display the person silhouette icon in all themes, including High Contrast.
- The fallback icon should render as a direct SVG element without any intermediate wrapper view.
- The icon's color should be inherited from the theme's foreground color tokens, so it adapts correctly in High Contrast mode.
- The icon dimensions should be explicitly derived from the size token rather than using relative percentage values.

## Why This Matters

Users who rely on High Contrast mode (often users with visual impairments) see blank Avatars instead of the expected person icon when no photo or initials are available. This is an accessibility regression that should be fixed by changing how the fallback icon is composed and rendered.
