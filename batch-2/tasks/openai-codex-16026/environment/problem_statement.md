## Description

The TUI status line and terminal title do not update automatically when the active model or collaboration mode changes. After switching to a different model or toggling into a planning-focused collaboration mode, the displayed model name and reasoning effort level remain stale until something else triggers a full re-render. This creates a confusing experience where the interface appears out of sync with the actual session state.

Additionally, several UI snapshot tests that contain working directory paths fail on Windows because the rendered paths use platform-specific formatting that differs from the stored snapshots.

## Expected Behavior

- When the active model is changed, the terminal title should immediately reflect the new model name without requiring any additional refresh step.
- When the collaboration mode is changed (for example, switching to a planning-focused mode), the status line should immediately display the correct reasoning effort level for the new mode. Switching to plan mode should show a medium reasoning effort; switching back to the default mode should restore the previous level.
- A new planning-focused collaboration mode ("plan mode") should be fully supported and its reasoning effort level should be correctly reflected in the status line.
- Snapshot tests should normalize working directory path representations before comparison so they produce consistent results on all operating systems.

## Why This Matters

Users rely on the status line and terminal title to know what model and mode is currently active. Stale display values erode trust in the interface and require workarounds like manually triggering refreshes. Cross-platform snapshot failures also block contributors who develop on Windows from running the test suite successfully.
