Implement a two-way conversion system between the visual editor's internal document representation and the MDX file format. Ensure that content authored in the editor can be saved as valid MDX files, and existing MDX files can be loaded into the editor for display or editing.

*   Implement `proseMirrorToMDXRoot` to convert a ProseMirror document into a valid MDX MDAST Root node.
    *   Serialize paragraphs and headings (with a level attribute) to MDX format.
    *   Handle nested unordered lists, ensuring correct MDX list syntax and indentation.
    *   Convert inline code marks to MDX inline code syntax, handling combined bold+code marks.
    *   Convert link marks to MDX link syntax, including links containing inline-code-marked text.
    *   Convert code block nodes with language and meta information, splitting the string for MDX output.

*   Implement `mdxToProseMirror` to parse an MDX MDAST Root into a ProseMirror document node.
    *   Convert MDX paragraphs to ProseMirror paragraph nodes.
    *   Convert MDX headings to ProseMirror heading nodes with level and props attributes.
    *   Parse unordered and ordered lists into ProseMirror list nodes, handling empty list items.
    *   Parse MDX inline code into text nodes with a code mark.
    *   Parse links into text nodes with a link mark, defaulting the title to an empty string if absent.
    *   Parse code blocks with language and meta information into a code_block node with a combined language attribute.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.