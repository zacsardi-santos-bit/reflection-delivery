## Description

The Markdown parser fails to correctly handle block-level constructs — headings and horizontal rules — when they appear inside blockquotes. After consuming the blockquote prefix character on each line, the lexer's internal state does not recognize the remaining content as being at the start of a line. As a result, sequences that should be recognized as heading underlines or thematic breaks are instead tokenized as plain text characters, causing the parser to produce incorrect output.

For example, a blockquote containing a text line followed by a line of three dashes should render as a level-two heading inside the blockquote. Instead, the parser treats the dashes line as plain paragraph text, dropping the heading structure entirely. Similarly, a standalone line of dashes inside a blockquote should produce a horizontal rule, but is currently parsed as a paragraph.

## Expected Behavior

- A text line followed by three or more dashes or equals signs inside a blockquote should produce a setext heading.
- A standalone thematic break pattern (three or more dashes, stars, or underscores, with or without spaces between them) on a quoted line should produce a horizontal rule.
- Mixed-character sequences combining different thematic break characters must NOT be treated as thematic breaks.
- Unspaced three-star sequences inside blockquotes must NOT be parsed as thematic breaks.
- Indented code blocks inside blockquotes must terminate properly when followed by a thematic break line, rather than absorbing the break as code content.
- These behaviors must also work correctly for nested blockquotes.
- The underlying lexer infrastructure must support re-lexing a token at the current position while treating that position as a line start, so the parser can correctly produce block-level tokens after consuming a blockquote prefix.

## Why This Matters

Blockquotes are common in Markdown documents, and headers and horizontal rules inside blockquotes are valid and frequently used constructs according to the CommonMark specification. Without this fix, the parser produces incorrect output for valid Markdown, causing tools that rely on this parser to behave incorrectly.
