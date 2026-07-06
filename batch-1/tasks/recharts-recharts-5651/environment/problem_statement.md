# Scatter Chart: Incorrect Data Flashing When Data Key Changes

## Description

There is a timing bug in scatter charts where changing the data key or data source causes the chart to briefly render incorrect scatter point positions. When the scatter component's configuration changes (e.g., switching between two different data sets with different data keys), the point computation runs with the new settings before the chart's internal registration state has been updated to reflect those new settings. This mismatch produces a brief flash of incorrect data.

## Expected Behavior

- When a scatter chart initializes or its configuration changes, scatter points should **not** be rendered until the chart's internal state is synchronized with the new configuration.
- On the first render, before the scatter component has registered itself in the chart state, the computed scatter points should be absent (no data to display yet).
- Once the chart state is synchronized, the correct scatter points should be computed and rendered.
- The computed points should remain stable across re-renders when the configuration has not changed.

## Why This Matters

Users switching between different datasets in a scatter chart see a brief flash of wrong positions before the correct data appears. This is visually jarring and can mislead users into thinking data changed in unexpected ways. The fix ensures scatter points are only computed once all parts of the chart are in a consistent state.
