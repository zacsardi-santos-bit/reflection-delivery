## Description

The admin metrics dashboard currently shows raw internal metric identifiers as the names of each metric in the table. These identifiers are meaningful to developers but confusing for administrators who just want to understand system health at a glance.

We should replace these raw identifiers with descriptive, human-readable labels so the metrics page is immediately understandable without requiring deep knowledge of the underlying system.

## Expected Behavior

- Each metric should appear with a friendly display name rather than its raw technical key.
- The filtering and dropdown-based selection in the metrics table should work correctly with the new display names.
- Metrics that belong to certain internal subsystems (authentication, multiprocessing, and garbage collection) must continue to be excluded from display.

## Additional Work

As part of this effort, a new general-purpose paginated table component should be introduced that can be reused across the application. This component should support:
- Rendering tabular data with configurable columns
- Row selection via checkboxes, with a callback receiving the selected rows
- Clickable rows with a customizable per-row click handler
- Optional pagination controls that automatically hide when there are no pages to show

## Why This Matters

Administrators using the metrics page should be able to understand what each metric means without needing developer context. Clearer labels make the admin interface more accessible and reduce the chance of misinterpretation.
