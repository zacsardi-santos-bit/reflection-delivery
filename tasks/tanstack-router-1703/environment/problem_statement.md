## Description

There is a bug in relative path resolution when a navigation link or programmatic navigation specifies an ancestor/parent route as the starting point.

When a component is rendered at a deeply nested route and uses navigation with a starting point set to a parent route path, and a relative destination path, the relative path is incorrectly resolved from the **currently active route** instead of from the specified starting route. This causes navigation to go to the wrong destination.

**Example of the broken behavior:**
- Current active route: the "details" sub-route of a parameterized post route
- Specified starting point: the parent parameterized post route
- Relative destination: a sibling route called "info"
- **Wrong result**: Navigates to a deeply nested path (resolving from the active route)
- **Expected result**: Navigates to the correct sibling route (resolving from the specified ancestor)

Similarly, parent-relative path traversal goes up from the wrong starting point, producing incorrect destinations.

Additionally, when the specified starting route path does not match any route in the current active hierarchy, there is no clear error — the application silently behaves incorrectly.

## Expected Behavior

- When the navigation starting point is set to an ancestor of the active route, relative destination paths must be resolved starting from that ancestor, not from the currently active route.
- When the specified starting route is not part of the active route hierarchy, a clear error must be produced immediately with a descriptive message indicating which starting route could not be matched.

## Why This Matters

This bug makes it very difficult to write reusable navigation links and components inside child routes that need to navigate relative to a specific, known ancestor route. It is also a source of silent, hard-to-debug incorrect navigation behavior.
