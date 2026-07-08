I'm extending the start coordinates settings panel in the interactive graph editor. Right now it only handles linear and ray graph types, but authors editing segment graphs or linear system graphs have no way to set where those elements first show up on the canvas, which limits how intentional their exercises can be.

For segment graphs I want the panel to render a labeled section per segment, so "Segment 1", "Segment 2" and so on, and inside each one the two endpoint coordinates need to be editable with "Point 1" and "Point 2" labels. When any coordinate changes I need the full updated array of coordinate pairs across all segments handed back to the calling component through the change callback (not just the one that moved). And there should be a reset button reading "Use default start coords" that puts everything back to the default positions.

Linear system graphs should work basically the same, labeled sections per line ("Line 1", "Line 2") with the same "Point 1"/"Point 2" endpoint editors and the same reset behavior.

Also the collapsible "Start coordinates" heading needs to actually be a real accessible button element you can toggle open and closed. This matters because when that section is expanded its coordinate inputs currently clash with other controls (like the locked figures settings panel), so making the heading collapsible lets those other panels be used cleanly, oh and it fixes the accessibility gap too.

One more thing, I need a little utility for deep-copying coordinate arrays, put that in its own dedicated utility module rather than inline.
