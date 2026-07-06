## Description

The CLI's built-in text editing mode that mimics a well-known terminal editor is missing several fundamental character-level editing and navigation commands that experienced users expect. Specifically, the following capabilities are absent:

- Deleting the character **before** the cursor (the uppercase delete command)
- **Toggling the case** of characters at or after the cursor position
- **Replacing** a character in place by pressing a key sequence to specify the replacement
- **Jumping the cursor** to a specific character ahead or behind on the current line (including a "stop just before the target" variant)
- **Deleting text** from the current cursor position to a target character (forward and backward, both inclusive and exclusive)

Without these commands, users who rely on the editing mode for efficient text manipulation find themselves missing core parts of the expected editing experience.

## Expected Behavior

- Users can delete the character(s) immediately before the cursor with an optional numeric repeat count
- Users can toggle the case of one or more characters at the cursor, with the cursor advancing past the affected range
- Users can replace one or more consecutive characters by specifying a replacement character; pressing Escape cancels the pending replacement
- Users can jump the cursor to the Nth occurrence of a character forward or backward on the current line, including a variant that stops just before or just after the match
- Users can repeat the last character-search jump in the same or opposite direction
- Users can delete from the cursor to a forward or backward character search target (inclusive or exclusive), including when combined with the change operator (which also enters insert mode)
- All count-prefixed variants work correctly, the commands that modify text are repeatable via the standard repeat mechanism, and all operations handle Unicode and multi-byte characters correctly

## Why This Matters

These commands are among the most frequently used in interactive terminal text editing. Their absence makes the editing mode feel incomplete and forces users to work around limitations that they would not encounter in a full editor.
