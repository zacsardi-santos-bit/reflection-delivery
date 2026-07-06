Implement the function `$setBlocksType` to correctly convert an empty list item into a paragraph outside the list structure in the Lexical editor. Ensure the new paragraph maintains the correct indentation and allows normal typing and paragraph creation.

*   Update the `$setBlocksType` function in `packages/lexical-selection/src/range-selection.ts` to handle the conversion of a list item to a paragraph:
    *   Ensure that when `$setBlocksType` is invoked with a `createElement` function (e.g., `$createParagraphNode`) and the selection is on an empty list item following a nested list, the new block element is placed outside the list structure.
    *   Preserve the existing list structure above the cursor, maintaining the hierarchy and content of list items.
    *   Convert the empty list item to a paragraph with the same indentation level as the original list item.
*   Ensure the resulting paragraph:
    *   Is rendered as `<p style='padding-inline-start: calc(1 * 40px)'><br /></p>` before any text is inserted, reflecting the inherited indent level.
    *   Allows text insertion and subsequent paragraph creation with Enter, where:
        *   The first paragraph retains `dir='ltr'` and `style='padding-inline-start: calc(1 * 40px)'`.
        *   The new paragraph created by pressing Enter has `dir='ltr'` and no `padding-inline-start` style.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.