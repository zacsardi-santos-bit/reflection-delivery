## Description

When dashboards are taller than the browser viewport, the current screenshot mechanism only captures the visible portion of the page. For tall dashboards with many charts, this results in incomplete screenshots that cut off content below the fold.

We need a way to capture the full height of a dashboard by automatically scrolling through it in increments, taking a screenshot at each scroll position, and stitching those tiles together into a single complete image.

## Expected Behavior

- The system should scroll through the dashboard in viewport-sized increments, capturing each section as a separate tile.
- All tiles should be combined vertically into a single full-height PNG image (width = max of all tile widths, height = sum of all tile heights).
- A single tile should be returned as-is without unnecessary processing.
- An empty set of tiles should return an empty result.
- If the image stitching encounters an error, the first valid tile should be returned as a fallback, and the error should be logged.
- If the target element cannot be located on the page, the operation should return nothing gracefully rather than crashing.
- Any unexpected errors should be caught, logged with a descriptive message, and cause the function to return nothing.
- The system should log the dashboard dimensions and the number of tiles being captured.
- After capturing all tiles, the page scroll position should be reset to the top.

## Why This Matters

Automated dashboard exports and email reports that include screenshots currently produce incomplete images for tall dashboards. This feature ensures that users and recipients always receive a full snapshot of the entire dashboard regardless of its height.
