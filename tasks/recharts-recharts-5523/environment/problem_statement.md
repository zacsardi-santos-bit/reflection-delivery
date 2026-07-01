## Description

When a chart is placed below the top of the page — because the page has been scrolled or the chart lives inside a container with a vertical offset — hovering over the chart highlights the wrong data point and positions the tooltip incorrectly. The root cause is that the conversion from a raw mouse event position to a chart-relative coordinate was not accounting for how far the chart element itself is offset from the top of the page.

## Expected Behavior

- When a user hovers over a data point in a chart that is vertically offset on the page, the correct data point should become active.
- The internal coordinate used to determine which data point is active should subtract the chart element's vertical offset from the raw mouse position, not use the raw position directly.
- The chart element's vertical and horizontal offset within the page layout should be available to the coordinate calculation so both axes can be corrected.

## Why This Matters

Charts placed lower on a page or inside scrolled containers are extremely common. Without this fix, any such chart will consistently activate the wrong data point on mouse hover, making tooltips unreliable and the interactive chart experience broken for a large share of real-world use cases.
