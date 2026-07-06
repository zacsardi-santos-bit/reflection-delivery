## Description

The markdown import and export utilities have two related whitespace-handling bugs that cause certain content to be silently corrupted during round-trips through the editor.

**Bug 1: Trailing whitespace stripped in non-merging mode**

When markdown is normalized without the line-merging option, trailing spaces on content lines are removed before the content is imported. This means that paragraphs with intentional trailing whitespace lose those spaces after a round-trip. For example, a paragraph like "hello world   " (with three trailing spaces) followed by a blank line should preserve those trailing spaces across import and export, but currently they are discarded.

**Bug 2: Backslashes double-escaped on export with preserve-newlines option**

When exporting markdown with the option to preserve structural newlines, backslash characters in text content are unnecessarily escaped. This corrupts content that uses backslash-terminated hard line breaks: importing and re-exporting such content with the preserve-newlines option produces double-escaped backslashes, changing the meaning of the source.

## Expected Behavior

- When normalizing markdown without line merging, trailing whitespace on non-empty content lines must be preserved (only whitespace-only lines should collapse to empty).
- A round-trip through import and export in default mode should preserve trailing whitespace in paragraph text.
- A round-trip through import and export with the preserve-newlines option enabled should preserve backslash-terminated hard line breaks exactly as written.

## Why This Matters

Applications that use the editor to load and save markdown source need to faithfully round-trip content. Losing trailing spaces or corrupting backslash line breaks degrades the fidelity of the markdown, which is especially problematic for users or tools that depend on these whitespace conventions for formatting.
