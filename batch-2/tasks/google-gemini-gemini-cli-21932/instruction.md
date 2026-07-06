Implement several new character-level editing and navigation commands in the CLI's built-in text editing mode. These commands should mimic those found in a classic terminal editor, enhancing the user experience by providing expected functionalities.

*   Implement the 'delete character before cursor' action:
    *   Accept a count parameter.
    *   Delete count characters immediately before the cursor.
    *   Move the cursor left by the number of characters deleted.
    *   Clamp to column 0 if count exceeds the cursor position.
    *   Do nothing if the cursor is already at column 0.
    *   Push the prior state onto the undo stack.

*   Implement the 'toggle case' action:
    *   Accept a count parameter.
    *   Toggle the case of count characters starting at the cursor.
    *   Advance the cursor to cursorCol + count, clamped to the last valid column.
    *   Do nothing if the cursor is past the end of the line.
    *   Push the prior state onto the undo stack.

*   Implement the 'replace character' action:
    *   Accept a char and a count parameter.
    *   Replace count consecutive characters starting at the cursor with the given char.
    *   Clamp count to the number of remaining characters on the line.
    *   Place the cursor on the last replaced character.
    *   Do nothing if the cursor is past the end of the line.
    *   Push the prior state onto the undo stack.

*   Implement 'find character forward' and 'find character backward' actions:
    *   Accept a char, count, and till (boolean) parameter.
    *   Forward search starts from cursorCol+1; backward search starts from cursorCol-1.
    *   Move the cursor based on the till parameter.
    *   Handle Unicode and multi-byte characters correctly.

*   Implement 'delete to character forward' action:
    *   Accept char, count, and till parameters.
    *   Delete text from the cursor to the found character based on the till parameter.
    *   Move the cursor to the start of the deleted region.
    *   Push to the undo stack.
    *   Handle Unicode and multi-byte characters correctly.

*   Implement 'delete to character backward' action:
    *   Accept char, count, and till parameters.
    *   Delete text from the found character to the cursor based on the till parameter.
    *   Move the cursor to the start of the deleted region.
    *   Push to the undo stack.

*   Extend the buffer object with new methods:
    *   `vimDeleteCharBefore(count: number): void`
    *   `vimToggleCase(count: number): void`
    *   `vimReplaceChar(char: string, count: number): void`
    *   `vimFindCharForward(char: string, count: number, till: boolean): void`
    *   `vimFindCharBackward(char: string, count: number, till: boolean): void`
    *   `vimDeleteToCharForward(char: string, count: number, till: boolean): void`
    *   `vimDeleteToCharBackward(char: string, count: number, till: boolean): void`

*   Update NORMAL mode keybindings:
    *   `X` calls `vimDeleteCharBefore(count)`.
    *   `~` calls `vimToggleCase(count)`.
    *   `r` initiates a two-key sequence for `vimReplaceChar`.
    *   `f/F/t/T` initiate two-key find sequences.
    *   `;` repeats the last find operation.
    *   `,` repeats the last find in the opposite direction.
    *   `d` and `c` with `f/t/F/T` and char initiate delete/change sequences.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.