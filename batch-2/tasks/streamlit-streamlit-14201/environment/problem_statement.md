## Description

The main menu component's test utilities currently rely on internal DOM identifiers to locate elements during tests. These identifiers have no accessibility meaning — they are not visible to screen readers or assistive technologies, and they tightly couple tests to internal implementation details. When a team member opens the menu, collects item labels, or interacts with menu elements in tests, they must manually thread a rendered component reference through every helper call, which is verbose and error-prone.

The test helper that collects visible menu item labels should be able to locate the menu container on its own — using the same semantic signals (roles and names) that a real user with assistive technology would rely on. Similarly, the helper that opens the menu should find the trigger button by its accessible label, not by an internal identifier.

## Expected Behavior

- The helper function that collects menu item labels should require no external component reference; it should locate the menu container automatically by its accessible role and name.
- The helper function that opens the menu should locate the menu trigger button by its accessible role and accessible name.
- The main menu button must expose an accessible name of "Main menu", announce that it controls a popup menu, and reflect whether the menu is currently open or closed.
- The menu container must expose the "menu" role and an accessible name of "Main menu".
- Each action item in the menu must expose the "menuitem" role with its label as the accessible name.
- The auto-rerun toggle must expose the checkbox menuitem role with a name containing "Auto rerun".
- Theme options must expose the radio menuitem role with their theme name, inside a group labeled "Theme".
- Divider elements must expose the separator role.
- Disabled items must announce their disabled state.

## Why This Matters

Using role and name-based queries rather than internal identifiers makes tests more meaningful: they implicitly verify that every tested interaction is accessible to users of assistive technologies, and they remove boilerplate from test code by eliminating the need to pass component references to shared helpers.
