I'm building a rich text editor plugin for a two-column table component where each row has a label cell and a value cell, and I keep hitting a bunch of editing bugs that leave the structure broken. Can you help me fix the split/merge/delete/insert behaviors?

First problem, when I press Enter inside a cell that mixes bold and regular text, the row splits but the formatting gets flattened to plain text. I want the split to preserve inline formatting (bold and whatever else) on both halves, so the new row's cell ends up with the correctly styled text nodes on each side of the cursor.

Second, pressing Backspace at the very start of a row's first column, or Delete at the very end of the last column, should merge the current row with its neighbor. Right now that merge falls apart when one of the relevant cells in the adjacent row is empty, content gets lost or the cursor lands somewhere weird. It should still join the rows correctly with the matching column content concatenated, even when a cell is empty.

Third, selecting across multiple cells or rows and deleting often leaves me with missing cells or content in the wrong place. Delete should always collapse the selection while keeping the two-column structure intact, so the text before the selection start and the text after the selection end each land back in their correct respective columns. This needs to hold no matter which cell the selection starts and ends in (label-to-value, value-to-value, label-to-label, value-to-label) and whether the selection was made forward or backward.

Last thing, there's no support for inserting multiple rows at once, like from a paste. When several rows come in as a fragment they should get placed after the current row, and if any inserted row is missing one of the two columns, add a blank cell automatically so the two-column structure stays consistent.

Without these fixes people editing table content constantly end up losing formatting, wrecking the cell structure, or just not being able to paste copied rows, and then they're stuck undoing or manually repairing everything. I'd like all four to work together cleanly.
