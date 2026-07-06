## Description

Several correctness issues affect interactive selections on Plotly charts embedded in Marimo notebooks, particularly for line charts, bar charts, and charts with datetime or categorical axes.

**Line chart selection ignores y-axis range:** When a user draws a box selection on a pure line chart, the selection currently includes all data points whose x-coordinate falls within the drawn range, regardless of their y-position. This means selecting a small region near the bottom of the chart still returns points with y-values far outside the visible selection box. The selection should only include data point vertices that fall inside both the x and y bounds.

**Lasso selection on line charts is broken:** Drawing a lasso on a pure line chart should return only the vertices enclosed by the lasso polygon, but currently line segments that merely pass through the region are incorrectly included.

**Clicking a data point on a line chart does not register:** A direct click on a point in a pure line chart should report that point as the selection, but click events are not currently handled for line traces.

**Bar chart selection produces duplicates:** When the frontend already provides explicit selected points, the backend extraction logic may duplicate them, returning the same point twice.

**Bar chart ignores malformed entries:** When the selection payload contains empty placeholder entries in the points list, they should be silently filtered out and the reported indices should remain aligned with only the valid entries.

**Non-numeric bar values cause crashes:** Range-overlap checks for bar charts raise exceptions when bar values or base values are non-numeric strings, instead of gracefully returning a negative result.

**Datetime handling is timezone-dependent:** Naive datetime objects in selection ranges are interpreted differently depending on the server's timezone configuration, leading to inconsistent results across environments.

## Expected Behavior

- Box selections on line charts return only vertices inside both x and y bounds
- Lasso selections on line charts return only vertices inside the polygon
- Click events on line chart points return the clicked point as the selection
- Bar chart selections with pre-provided points are not duplicated
- Empty point entries in bar selections are filtered out with proper index alignment
- Non-numeric bar values are handled gracefully without raising errors
- Naive datetimes are treated as UTC consistently, regardless of server timezone

## Why This Matters

Users relying on chart selections to drive downstream computations in their notebooks get incorrect data back when using line charts or encountering edge cases with bar charts and datetime axes, making the selection feature unreliable in practice.
