## Description

Gitea bundles a set of icon assets from the upstream Octicons library for use throughout the interface. As the upstream library gains new icons, Gitea's bundled set needs to be updated to include them. Several icons that exist in recent versions of Octicons are not yet present in Gitea's SVG assets directory, making them unavailable for any UI feature that wants to use them.

## Expected Behavior

The following icons should be available as SVG files in Gitea's public SVG assets directory:

- Accessibility inset icon
- AI model icon
- Bookmark filled icon
- Bookmark slash fill icon
- File media icon
- Home fill icon
- Tab icon

## Why This Matters

Without these files, any part of the UI that references these icons would encounter missing assets, resulting in broken or blank icon display. Adding these files keeps Gitea's icon set in sync with the upstream Octicons library and allows developers to use these new icons in templates and components.
