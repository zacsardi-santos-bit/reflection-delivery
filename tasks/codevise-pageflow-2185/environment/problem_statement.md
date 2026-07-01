## Description

The editable table component provides a two-column layout (a label column and a value column per row), but several common editing operations do not correctly maintain that structure or preserve text formatting.

### Issues

1. **Formatting lost on row split**: When a user presses Enter inside a cell whose content mixes bold and regular text, the new row is created but the formatting of the split text is not preserved — all text becomes plain.

2. **Row merge broken when one column is empty**: When a user presses Backspace at the very start of a row, or Delete at the very end, the editor should merge the current row with its neighbor. However, when one of the cells in the adjacent row is empty, the merge does not happen correctly — content may be lost or the cursor ends up in the wrong place.

3. **Selection delete breaks the two-column structure**: When a user selects content that spans across two or more rows (in any combination of label and value cells) and then deletes it, the resulting editor state may have incorrect or missing cells. The two-column structure should always be maintained after any delete operation, with the remaining text from the selection endpoints correctly joined in their respective columns.

4. **Pasting multiple table rows does not work**: When structured table content (multiple rows) is inserted, the operation is not handled — nothing is inserted or the result is malformed. Additionally, if pasted rows are missing one of the two columns, the editor should pad them with an empty cell automatically.

## Expected Behavior

- Splitting a row with formatted text should preserve bold and other inline formatting in both halves of the split.
- Deleting backward or forward when one adjacent row column is empty should still correctly merge the rows and join the content of the matching columns.
- Deleting a selection that spans cells or rows should always leave the editor in a valid two-column state, joining partial text from the selection boundaries into the appropriate columns.
- Inserting multiple rows at once should work correctly, with missing cells automatically filled with empty content.

## Why This Matters

Without these fixes, users editing structured table content frequently end up in broken editor states — losing formatting, accidentally deleting cell structure, or being unable to paste copied table rows — requiring them to undo or manually repair their content.
