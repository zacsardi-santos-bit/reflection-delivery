Implement a rich text editor plugin for a two-column table component to address specific editing issues. Ensure that operations such as splitting, merging, deleting, and pasting rows maintain the table's structure and preserve text formatting.

*   Implement `editor.insertBreak()`:
    *   Split cell content at the cursor while preserving text formatting.
    *   Place the first half of the text in the current row and the second half in a new row's same column.
    *   Set the cursor at path `[newRowIndex, columnIndex, 0]` offset 0.

*   Implement `editor.deleteBackward()`:
    *   Merge rows when the cursor is at the start of a value cell and the label cell is empty.
        *   Join value cell content and place the cursor at the end of the previous row's value text.
    *   Merge rows when the cursor is at the start of a label cell and the previous row's value cell is empty.
        *   Join label cell content and retain the current row's value, placing the cursor at the end of the previous row's label text.

*   Implement `editor.deleteForward()`:
    *   Merge rows when the cursor is at the end of a value cell and the next row's label cell is empty.
        *   Join value cell content and maintain the cursor at its current offset.
    *   Merge rows when the cursor is at the end of a label cell and the current row's value cell is empty.
        *   Join label cell content and take the next row's value, maintaining the cursor at its current offset.

*   Implement `editor.deleteFragment()`:
    *   Handle selections within the same row, maintaining the two-column structure.
    *   Manage cross-row selections for all combinations (label→value, value→value, label→label, value→label).
        *   Remove intermediate rows and merge text from selection endpoints into appropriate columns.
        *   Preserve text formatting on remaining text.
        *   Normalize selection direction to ensure consistent results.

*   Implement `editor.insertFragment(fragment)`:
    *   Insert text content when the fragment has one row with one cell, placing the cursor at the end of the inserted text.
    *   Insert multiple rows or a row with multiple cells as new rows after the current row, placing the cursor at the end of the last inserted row.
    *   Prepend an empty label cell if the first fragment row is missing one.
    *   Append an empty value cell if the last fragment row is missing one.

*   Ensure `withFixedColumns(editor)` returns an editor with overridden methods for table-specific behaviors.
*   Maintain export of `handleTableNavigation` from the same module.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.