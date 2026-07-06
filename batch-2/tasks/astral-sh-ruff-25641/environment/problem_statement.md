## Description

The Python formatter normalizes comments by inserting a single leading space after the hash character when one is missing. However, certain special comment prefixes are intentionally exempt from this normalization and must be preserved verbatim, because external tools rely on the exact format of those comments.

Currently, the formatter already preserves shebang-style, Sphinx-style, pweave-style, and double-hash comments unchanged. However, it does not yet preserve pipe-prefixed comments (those with a pipe character immediately after the hash), which are used by some notebook and document authoring tools as structured cell-level metadata markers. When the formatter encounters a pipe-prefixed comment, it incorrectly rewrites it by inserting a space, breaking compatibility with those tools.

## Expected Behavior

- Pipe-prefixed comments (those starting with a pipe character immediately after the hash, with no intervening space) should be left completely untouched by the formatter, just like the other special comment prefix types.
- This preservation should apply when formatting both standalone Python files and Python code blocks embedded in Quarto markdown documents.
- Python code within the same block as these cell option comments should still be reformatted normally — only the pipe-prefixed comments themselves are exempt.

## Why This Matters

Quarto documents use pipe-prefixed comments to specify cell-level options (like whether to echo output, set figure dimensions, etc.). If the formatter modifies these comments, it breaks the document's functionality. Users formatting Quarto-related Python files or Quarto documents should be able to do so without the formatter corrupting their cell option annotations.
