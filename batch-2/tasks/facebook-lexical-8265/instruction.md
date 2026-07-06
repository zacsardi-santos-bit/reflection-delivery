I'm working with the markdown conversion utilities in lexical-markdown and I've found two bugs related to whitespace handling.

*   The normalizeMarkdown function, when called with shouldMergeAdjacentLines=false, must preserve trailing whitespace on non-empty content lines. For example, 'foo   \n\nbar' must remain 'foo   \n\nbar' after normalization — the trailing spaces on 'foo   ' must not be stripped.

*   The normalizeMarkdown function must continue to collapse whitespace-only lines to empty strings regardless of the shouldMergeAdjacentLines flag. For example, 'A\n   \nB' must normalize to 'A\n\nB' even when shouldMergeAdjacentLines=false.

*   The normalizeMarkdown function must preserve leading whitespace on non-empty content lines when shouldMergeAdjacentLines=false. For example, '   foo\n\nbar' must remain '   foo\n\nbar'.

*   When exporting markdown with shouldPreserveNewLines=true, backslash characters in text content must not be escaped. This ensures backslash-terminated hard line breaks survive a round-trip: importing 'foo\\\nbar' and re-exporting with shouldPreserveNewLines=true must return 'foo\\\nbar' unchanged.

*   When importing markdown in default mode (shouldPreserveNewLines not specified) and then exporting it, trailing whitespace in standalone paragraph content must be preserved. For example, 'hello world   \n\nnext paragraph' must round-trip and produce the exact same string.


*   Interface details: Type: Function
Name: normalizeMarkdown
Location: packages/lexical-markdown/src/MarkdownTransformers.ts
Signature: normalizeMarkdown(markdown: string, shouldMergeAdjacentLines: boolean): string
Description: Normalizes a markdown string by processing line breaks and whitespace. When shouldMergeAdjacentLines is true, adjacent non-empty lines are merged with a single space (trimming hard-break trailing spaces). When shouldMergeAdjacentLines is false, lines are not merged and trailing whitespace on non-empty content lines must be preserved; whitespace-only lines still collapse to empty strings. Code block contents are preserved verbatim (including trailing whitespace) in both modes.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.