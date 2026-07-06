## Description

The network tab in the developer tools console displays a table of HTTP requests made during a session, but the column headers are not interactive — users cannot sort the list by any column. When there are many requests, it's difficult to quickly find requests by method type, status code, or duration without manually scanning every row.

## Expected Behavior

- Clicking a column header should sort the request list by that column in ascending order.
- Clicking the same column header a second time should switch to descending order.
- Clicking the same column header a third time should clear the sort and restore the original insertion order.
- Clicking a different column header while another column is sorted should reset to ascending on the newly selected column.
- A directional arrow icon should appear on the active sort column to indicate the current sort direction, and this icon should only appear on one column at a time.
- Method sorting must be case-insensitive (lowercase and uppercase method names should sort identically).
- Status sorting should be by numeric status code.

## Why This Matters

Without sortable columns, developers debugging API sessions must manually scan request lists to identify patterns — for example, finding all failing requests or grouping by method type. Sortable columns make the network tab significantly more useful for debugging and analysis.
