## Description

The visual editor for MDX-based content collections currently has no way to convert its internal document representation to and from the MDX file format. This means content authored in the editor cannot be saved as valid MDX files, and existing MDX files cannot be loaded into the editor for display or editing.

## Expected Behavior

- Editor content (paragraphs, headings, ordered and unordered lists, inline formatting, links, code blocks) can be converted to valid MDX output.
- Valid MDX input can be loaded and parsed into the editor's internal document format, preserving all common content types.
- Nested lists, empty list items, inline code, bold text, combined marks (e.g. bold and code together), and links containing formatted text all round-trip correctly between the editor format and MDX.
- Code blocks that specify both a language identifier and additional meta information on the fence line are preserved through the conversion.
- Links parsed from MDX carry both a URL and a title field (defaulting to an empty string when no title is present).

## Why This Matters

Without this two-way conversion, MDX-based collections in the CMS are unusable in the visual editor. Authors cannot load their existing MDX content for editing, and any changes they make cannot be persisted back to MDX files. Adding serialization and parsing support makes MDX collections fully functional in the visual editor.
