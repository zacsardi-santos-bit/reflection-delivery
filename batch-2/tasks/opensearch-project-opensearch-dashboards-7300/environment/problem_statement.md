## Description

OpenSearch Dashboards is introducing a new navigation mode that organizes navigation links by use case (e.g., Observability, Analytics, Management) rather than a single flat list. The current navigation sidebar has no concept of use case grouping, and the header has no way to switch to this new navigation style when the feature is enabled.

We need to:

1. Add support for registering and retrieving navigation controls positioned at the bottom of the expanded left navigation sidebar.
2. Build a new collapsible navigation component that, when activated, displays links organized by use case groups, with an overview that shows a few representative links per group along with a "See all…" option to drill into a specific group.
3. Build a top section for the new navigation that can display a home link, a back button (when inside a specific use case), and a collapse/expand button.
4. Update the header component to use the new navigation when the nav group feature flag is enabled, and to accept the additional observables and callbacks the new navigation requires.
5. Update core plugins (Dashboard, Discover, Index Pattern Management, Advanced Settings) to register their navigation links into the appropriate use case groups during plugin setup.

## Expected Behavior

- The navigation controls service exposes a new slot for registering bottom-left controls and an observable that emits those controls sorted by their order value.
- When the nav group feature flag is enabled, the header renders the new navigation component instead of the existing one.
- The new navigation component renders links for the current use case (or all groups in overview mode) and supports clicking through to a group and returning to the overview.
- The top section of the new navigation conditionally shows a home button, a back button, and a collapse/expand button based on the current navigation state.
- Core plugins correctly call the nav group registration API during setup so their links appear in the right places.

## Why This Matters

This capability is the foundation for the "navigation-next" feature, enabling users to navigate a large set of plugins and features organized by workflow rather than a flat, unordered list.
