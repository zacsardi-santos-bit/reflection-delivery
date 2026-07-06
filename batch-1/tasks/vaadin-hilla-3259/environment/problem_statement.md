## Description

When generating navigation menus from file-based routes, routes with dynamic URL segments (path parameters that match any value, like a record ID) are currently included in the menu output. This is incorrect behavior — dynamic routes don't represent fixed navigable destinations and should never appear as menu entries.

## Expected Behavior

- Routes whose path includes a variable segment (a segment that acts as a placeholder for any value) should be automatically excluded from the generated menu.
- All descendant routes of a variable-segment route should also be excluded, since their paths are inherently parameterized by the parent.
- This automatic exclusion should be in addition to the existing mechanism for explicitly opting a route out of the menu — the two features should coexist independently.
- When a route is explicitly opted out of the menu, only that route is hidden; its children remain in the menu. When a route is excluded because it has a variable segment, both it and all its children are excluded.

## Why This Matters

Navigation menus should only contain links to pages that can be reached without knowing a specific record ID or other dynamic value. Including parameterized routes in a menu leads to broken or unusable navigation items, since there is no concrete URL to navigate to.
