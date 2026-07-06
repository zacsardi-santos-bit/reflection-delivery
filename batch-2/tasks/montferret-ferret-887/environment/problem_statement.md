## Description

The query language formatter does not correctly respect the configured maximum line width when deciding whether to keep an expression on a single line. When content is already indented — for example, an object or array nested inside another structure — the formatter only measures the candidate inline expression's own length and compares it to the total print width. It ignores the column offset already consumed by indentation on the current line. This means that nested objects or arrays can end up formatted inline even though they would visually overflow the allowed line width.

Additionally, arrays that contain object literals as elements are being collapsed onto a single line when they fit. The desired behavior is that arrays containing structured elements (objects, nested arrays) should always expand to multiple lines with each element on its own indented line.

There is also a related issue in the low-level output component: it does not track the current column position as output is written. This means the information needed to make a correct inline-fit decision is simply unavailable.

## Expected Behavior

- When the formatter checks whether an expression fits on one line, it must account for how far along the current line the printer already is (including any leading indentation).
- Arrays containing object literals or other structured elements must always be rendered in multiline format, with each element indented on its own line.
- Object literals with many properties (more than four) must always be rendered in multiline format.
- A comment placed between an opening delimiter and the first element must force multiline format with the comment preserved on its own indented line.
- Formatted output should consistently end with exactly two trailing newlines.

## Why This Matters

Users relying on a configured print width to keep formatted output readable are surprised to see lines that exceed the limit when structures are nested. The formatter should produce output that genuinely respects the maximum line width at every level of nesting, not just at the top level.
