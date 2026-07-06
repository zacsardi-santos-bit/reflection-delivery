## Description

The deployments table currently has fixed, non-adjustable column widths. Users cannot resize columns to see more or less content based on their needs, and any manual adjustment attempt is lost on the next page load since there is no persistence.

## Expected Behavior

- Users should be able to drag the edge of a resizable column header to adjust its width in real-time.
- Resized column widths should be saved automatically and restored the next time the page is loaded, so customizations persist across sessions.
- Double-clicking a column's resize handle should reset that column back to its default width.
- Not all columns need to be resizable — the actions column, for example, should remain fixed-width with no resize handle.
- Resize handles should only appear for columns that are configured to be resizable; non-resizable columns must not display any resize handle.

## Why This Matters

Without resizable columns, users viewing deployments with long names or many tags are forced to work with a layout that may not suit their data. Persistent column sizing allows each user to customize the table to their workflow, and the ability to reset to defaults ensures they are never stuck in a bad layout.
