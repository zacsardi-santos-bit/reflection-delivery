## Description

The interactive graph editor lets content authors configure "start coordinates" — the initial positions of graph elements before a student interacts with them. This setting panel currently only works for linear and ray graph types. Authors editing segment graphs or linear system graphs have no way to configure where the segments or lines should initially appear on the graph canvas.

## Expected Behavior

- When editing a segment graph, the start coordinates panel should appear with a labeled section for each segment (e.g., "Segment 1", "Segment 2"). Each section should show the two endpoint coordinates for that segment and allow them to be edited.
- When editing a linear system graph, the start coordinates panel should appear with labeled sections for each line ("Line 1", "Line 2"), each showing its two endpoint coordinates.
- A reset button should restore all coordinates to their defaults.
- The "Start coordinates" panel heading should behave as an interactive control that can be collapsed and expanded, so it does not interfere with other interactive controls in the editor (such as locked figure settings).

## Why This Matters

Without this feature, authors are unable to control the initial state of segment and linear system graphs, limiting the quality and intentionality of exercises built with these graph types. Making the heading properly interactive also fixes an accessibility gap and resolves test interference with other editor UI panels.
