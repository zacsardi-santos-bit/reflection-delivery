I'm working on the interactive graph editor and need to extend the start coordinates settings panel to support segment graphs and linear system graphs. Right now the panel only works for linear and ray graph types, but we want authors to be able to configure where segment and linear system graph elements initially appear on the canvas.

For segment graphs, the settings should show a labeled section for each segment (for example "Segment 1", "Segment 2"), and within each section the two endpoint coordinates should be editable with "Point 1" and "Point 2" labels. When a coordinate changes, the full updated array of coordinate pairs for all segments should be passed to the calling component via the change callback. There should also be a reset button labeled "Use default start coords" that restores everything to the default positions.

For linear system graphs, the layout should be similar — show labeled sections for each line ("Line 1", "Line 2") with the same "Point 1" and "Point 2" endpoint editors and the same reset behavior.

Additionally, the collapsible "Start coordinates" heading needs to be updated to render as a proper accessible button element so it can be toggled open and closed. This is also important because when the start coordinates section is expanded, its coordinate inputs currently conflict with other controls in the editor (such as the locked figures settings panel), so the heading needs to be collapsible to allow those other panels to be used cleanly.

There is also a small utility needed for performing deep copies of coordinate arrays that should be placed in a dedicated utility module.
