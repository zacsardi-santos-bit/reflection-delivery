I'm working on a rich text editor and I've noticed a frustrating bug.

*   HeadingNode.collapseAtStart() must only convert the heading to a plain paragraph when the heading is completely empty; for non-empty headings it must return true immediately without modifying the heading or any surrounding nodes.

*   HeadingNode.collapseAtStart() must NOT convert a non-empty heading to a paragraph, even if it replaces it with a heading node of the same tag. The node must be left entirely unchanged.

*   The backward-delete (Backspace) operation at position 0 of a block must be updated so that, when the immediately adjacent block in the backward direction is empty and shares the same parent as the current block, the empty adjacent block is removed rather than merged. The current block must be preserved with its original type and full text content.

*   This 'remove empty adjacent block' behavior must apply regardless of the current block's type — it must work for headings, quotes, and other element node types alike.

*   The 'remove empty adjacent block' behavior must only apply when the adjacent block and the current block share the same parent. Structural containers (such as a list node whose only child is an empty list item) are not considered empty for this purpose; they must continue to use the default cross-block merge behavior.

*   When the preceding sibling is a non-empty block, the existing cross-block merge behavior must continue to apply: the current block's content is merged into the predecessor and the current block's type is discarded.

*   When Backspace is pressed at the start of a heading that is preceded by an empty paragraph, and there are additional blocks after the heading, only the empty paragraph must be removed. All other blocks (the heading and any following siblings) must be preserved unchanged.

*   HeadingNode.collapseAtStart() must still convert an empty heading to a paragraph — a heading containing no children must be replaced with a ParagraphNode whose children (if any) are moved over.

*   When HeadingNode.collapseAtStart() is called on a non-empty heading that is nested inside another ElementNode wrapper, it must leave the heading unchanged.

*   When HeadingNode.collapseAtStart() is called on a non-empty heading that has no previous sibling at its nesting level, it must leave the heading unchanged.

*   The moveToLineBeginning helper must be exported from the keyboard shortcuts module at packages/lexical-playground/__tests__/keyboardShortcuts/index.mjs so that end-to-end tests can move the cursor to the beginning of a line before triggering Backspace.


*   Interface details: Type: Method
Name: collapseAtStart
Location: packages/lexical-rich-text/src/index.ts
Signature: collapseAtStart(selection?: RangeSelection): boolean
Description: Instance method on the HeadingNode class. Called when the cursor is at the very start of a heading and a backward delete is triggered with no eligible previous block to merge into. The updated implementation must behave as follows:
  1. If the heading is empty: replace it with a plain ParagraphNode (existing behavior — converts empty heading to paragraph).
  2. If the heading is non-empty: return true immediately without modifying the heading or any other node. Do NOT convert a non-empty heading to a paragraph, and do NOT attempt to remove or move siblings.
  NOTE: The removal of an empty preceding paragraph when Backspace is pressed is handled in the selection-level backward-delete logic, NOT in this method. This method is only invoked by deleteCharacter when there is no previous block to merge with.

Type: Function
Name: moveToLineBeginning
Location: packages/lexical-playground/__tests__/keyboardShortcuts/index.mjs
Signature: moveToLineBeginning(page: Page): Promise<void>
Description: A Playwright end-to-end test helper that moves the editor cursor to the beginning of the current line. Must be exported from the keyboard shortcuts index module so e2e tests can import and use it to position the cursor before triggering Backspace.

Additional implementation note (not a new named interface):
The backward-delete (Backspace) behavior in packages/lexical/src/LexicalSelection.ts must be updated to handle the case where the adjacent (previous-direction) block is empty and shares the same parent as the current block. In this case, instead of performing a cross-block merge, the empty adjacent block should be removed and the operation should return early. This ensures the current block (e.g., heading or quote) is preserved with its original type. This check must only apply when the adjacent block shares the same parent as the current block — structural wrappers such as a ListNode containing an empty ListItemNode are not treated as empty and must continue to use the default cross-block merge.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.