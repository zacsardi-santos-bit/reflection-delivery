## Description

Several UI components in the design system (dropdown menus, popovers, tooltips, help buttons, overlay panels) are in the process of being upgraded to a new version of the underlying popover engine. The new implementation behaves differently in a test environment — in particular, it relies on browser APIs (like intersection detection) that are not available in the test runner, which causes rendered elements to be hidden rather than visible.

Currently, tests cannot easily opt-in to the new popover behavior: they have no way to render components with a specific feature flag enabled to exercise the new code path. This means the test suite cannot validate the new popover implementation, and assertions that expect elements to be visible (rather than simply present in the document) will fail under the new implementation.

## Expected Behavior

- There should be a shared testing utility that renders any component with a specified feature flag enabled, so that tests can exercise the new popover implementation.
- Tests that query for elements rendered by the new popover engine should be able to find hidden elements (since the new implementation hides elements due to missing browser API support in the test environment).
- Dropdown menu items should support a test identifier attribute so that keyboard navigation tests can reliably verify which item has focus, rather than relying on internal DOM element IDs.
- Snapshot tests for popover-based components should capture the DOM output produced by the new implementation.

## Why This Matters

Without this utility, validating the new popover engine in tests requires duplicating boilerplate in every test file, and existing tests will fail or produce misleading results when the new implementation is active. The shared utility makes it straightforward to write tests that cover both the current and updated behavior.
