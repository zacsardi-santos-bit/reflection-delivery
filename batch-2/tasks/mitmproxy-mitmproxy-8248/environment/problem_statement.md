## Description

In mitmweb's HTTP content viewer, blank lines within message bodies are not being displayed correctly. When a request or response body contains empty lines, they collapse and become invisible — rendered as empty, self-closing elements rather than visible blank lines. This makes it impossible to distinguish how many blank lines are present or where they appear in the content.

## Expected Behavior

- Blank/empty lines in the content viewer should be rendered as visible elements that occupy space on the page
- Multiple consecutive blank lines should each appear as a distinct, visible line
- The content viewer should accurately reflect the whitespace structure of the HTTP message body, not silently collapse blank lines

## Why This Matters

Many data formats (such as HTTP itself, MIME messages, and various text protocols) use blank lines as meaningful structural delimiters. When debugging traffic in mitmweb, developers need to see the exact content of messages including blank lines. If the viewer collapses blank lines, the displayed content no longer accurately represents the actual message — making debugging unreliable and confusing.
