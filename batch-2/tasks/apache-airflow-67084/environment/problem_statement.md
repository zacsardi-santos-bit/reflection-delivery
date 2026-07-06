## Description

When multiple plugins are registered, all of their navigation links are currently collapsed into a shared "Plugins" dropdown submenu. This means there is no way for a plugin author to ensure their plugin's link is always visible in the main navigation toolbar — it always ends up hidden behind an extra click once a second plugin is present.

## Expected Behavior

- Plugin definitions should support an optional flag that, when set, causes the plugin's navigation link to be rendered directly on the main toolbar regardless of how many other plugins are registered.
- Non-flagged plugins should retain the existing behavior: if two or more remain after the flagged ones are separated out, they are grouped into the existing submenu. If only one non-flagged plugin remains, it should also appear directly on the toolbar (avoiding a one-item submenu).
- The REST API response for plugin items should expose this flag explicitly, defaulting to false for plugins that have not opted in.

## Why This Matters

Plugin authors who want to highlight important integrations or tools have no current way to guarantee visibility in the navigation bar. This change gives them the ability to "promote" a plugin link to the top level, making it immediately accessible without forcing users to open a dropdown. Existing plugins continue to work exactly as before.
