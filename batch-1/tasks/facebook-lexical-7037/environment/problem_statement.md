## Description

When working with nested lists in the Lexical rich text editor, converting the cursor's current block to a regular paragraph (e.g. after pressing Enter at the end of a nested list item) can produce an invalid document tree. Instead of placing the new paragraph outside the list at the correct level, it gets embedded inside a list item node — a container that is only supposed to hold list-compatible content. This silently corrupts the editor state and causes further editing operations to behave unexpectedly.

## Expected Behavior

- When the block type is changed to a paragraph while the cursor is positioned at an empty list item following a nested list, the resulting paragraph should be placed **outside** the list structure, not inside it.
- The existing list items and nested list hierarchy above the cursor should be preserved exactly as they were.
- The new paragraph should inherit the indent level of the original list item (e.g. one indent level of indentation).
- After the conversion, the user should be able to type normally in the paragraph and press Enter to create subsequent unindented paragraphs without issue.

## Why This Matters

Users who build documents mixing nested lists with regular paragraphs (a common pattern in outline-style editors) can silently corrupt their document. The resulting invalid structure may not be immediately visible but causes unpredictable behavior when editing continues. This fix ensures block-type conversions always produce a valid, well-structured document regardless of surrounding list nesting.
