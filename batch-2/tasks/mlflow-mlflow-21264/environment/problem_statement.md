## Description

When navigating between tabs on the experiment page, time range filter parameters (such as a selected time window) are only preserved when moving between traces-related tabs. Switching from a traces tab to any other section — or between non-traces sections — drops these time range parameters entirely, even though they are just as relevant on those other tabs.

## Expected Behavior

- When navigating **within** traces-related tabs (e.g. Overview, Sessions), all query parameters — including both time range filters and any traces-specific context — should be preserved.
- When navigating **away from** a traces-related tab to a non-traces tab (e.g. Datasets, Judges), time range filter parameters should still be carried over, but traces-specific context (like a selected trace identifier) should be dropped.
- When navigating **from** a non-traces tab to a traces-related tab, time range filter parameters should be preserved, but tab-specific context from the source tab (like a selected dataset) should be dropped.
- When navigating **between** non-traces tabs, the same rule applies: only time range parameters are preserved; other tab-specific parameters are dropped.

## Why This Matters

Users frequently set time range filters to narrow down their data view, and those filters are meaningful regardless of which section of the experiment page they are viewing. Having those preferences silently reset every time a user switches tabs is disruptive and forces repeated re-entry of the same filter selections. Time range context should follow the user across the entire experiment page.
