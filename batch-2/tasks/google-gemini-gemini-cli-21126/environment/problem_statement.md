## Description

The tool interaction telemetry has several issues that reduce the accuracy and usefulness of the data being recorded when the AI agent accepts file edits.

First, the accepted line count is calculated incorrectly — only added lines are being counted, when both added and removed lines should be summed to represent the true scope of a change.

Second, the programming language of the edited file is not included in the telemetry, even though this information is directly available from the file extension. Language information would make it easier to understand which file types the AI edits most frequently.

Third, a generic "unknown" interaction is being recorded for every accepted tool call, including tools that have nothing to do with file editing. This creates unnecessary noise in the telemetry data and makes it harder to analyze meaningful edit interactions.

## Expected Behavior

- When a file edit is accepted, the accepted line count should include both added and removed lines.
- When a file is edited, the programming language (detected from the file extension) should be included in the telemetry interaction record.
- Only file-editing tool interactions should generate interaction telemetry events; other tool types should be silently ignored.

## Why This Matters

Accurate telemetry is essential for understanding the scope and patterns of AI-assisted edits. Miscounted lines, missing language data, and spurious unknown interactions all degrade the quality of telemetry insights and make it harder to act on the data.
