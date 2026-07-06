## Description

The sidebar navigation buttons in the frontend are currently rendered with the application's primary accent color. This causes an undesirable visual effect where every nav button in the sidebar appears in a prominent, colored style — which is inconsistent with the intended neutral appearance of the sidebar.

All sidebar navigation buttons (pipeline list, experiments, runs, recurring runs, artifacts, executions, documentation, etc.) should inherit the surrounding text color rather than using the app-wide primary color. This will make the sidebar look visually cohesive and prevent nav buttons from competing visually with primary-colored interactive elements elsewhere in the UI.

Additionally, the documentation link in the sidebar should be confirmed to open in a new browser tab with appropriate link security settings, and the documentation URL should be sourced from a shared constants location rather than being hardcoded inline.

## Expected Behavior

- All sidebar navigation buttons must use an inherited text color, not the primary accent color.
- The documentation link must open in a new browser tab with proper security attributes.
- The documentation URL must reference a shared, exported constant so it can be reliably referenced in tests and across the codebase.

## Why This Matters

Using the primary color on every sidebar button creates visual noise and is stylistically inconsistent. The sidebar should have a subdued, neutral styling that does not draw undue attention, while the documentation link should follow security best practices for external links.
