## Description

MCP tool calls can report their progress while executing, but the current UI shows that progress in a very limited way: it appends a text fragment to the tool's description line (e.g., "My Tool (Downloading... - 42%)"). This approach is hard to read, visually inconsistent with how other progress is shown in the CLI, and doesn't convey at a glance how far along a task actually is.

## Expected Behavior

- When a tool is executing and has reported progress, the UI should display a proper visual progress bar using block characters below the tool header, instead of appending text to the description.
- When the tool knows its total expected work, the bar should show a percentage label (clamped to 100%).
- When no total is known, the bar should show a raw count as an indeterminate indicator without a "%" label.
- An optional status message from the tool should appear alongside the bar.
- Progress information should be passed through the data pipeline as raw values (current progress and optional total), not as a pre-computed percentage.
- The system should validate incoming progress values and silently discard events reporting nonsensical values such as negative numbers, infinity, or not-a-number.
- Progress state stored on an active call should be preserved when other fields (such as live output) are updated separately.
- The progress handler should ignore events for calls that are not currently executing, including unknown calls and already-completed calls.
- The progress listener should be cleaned up when the scheduler is disposed.

## Why This Matters

A visual progress bar is much more intuitive and readable than text appended to a tool name. Passing raw progress values instead of pre-computed percentages also allows the rendering layer to make better display decisions (e.g., choosing between determinate and indeterminate modes). The validation ensures the UI is never given impossible values.
