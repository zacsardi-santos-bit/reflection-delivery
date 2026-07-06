## Description

When a user selects a single marker on a Plotly scatter chart where multiple data points share the same x-coordinate, the selection incorrectly returns all points at that x-value instead of just the explicitly selected marker. This causes downstream data processing to receive far more points than the user intended to select.

## Expected Behavior

- When Plotly already provides an explicit list of selected points (as it does for charts with visible markers), those exact points should be returned without any additional expansion.
- The system should NOT search for and include additional data points that happen to share the same x-coordinate as a selected point.
- For line-only traces (where Plotly does not provide point-level selections), the existing range-based fallback should continue to work as before.
- In figures that combine both marker traces and line-only traces, marker trace selections should be preserved from Plotly's payload, while line-only trace selections should still use the range-based fallback.

## Why This Matters

Users relying on chart selections for data filtering expect to get back exactly the points they selected. When a dataset has repeated x-values (e.g., multiple measurements at the same time or category), the over-expansion silently returns unrelated data points, making selections unreliable. Fixing this makes marker selections trustworthy while preserving the correct behavior for pure line charts.
