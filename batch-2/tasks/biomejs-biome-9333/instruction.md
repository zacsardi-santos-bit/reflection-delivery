I'm running into a crash in the HTML formatter when I try to format HTML files that have inline elements immediately followed by self-closing void elements — things like a code snippet element or a bold element right before a line break or an image.

*   The HTML formatter must not panic or crash when it encounters an inline element (such as code, span, b, or a) whose closing tag is immediately followed by a self-closing void element (such as br or img).

*   When formatting HTML where an inline element is directly followed by a self-closing void element on the same line, the formatter must preserve them on the same line without inserting any whitespace, newlines, or other changes between them.

*   The formatted output for input containing patterns like an inline element closing tag immediately adjacent to a self-closing void element must be identical to the input — no reformatting should occur for this structure under CSS whitespace sensitivity mode with self-close void elements disabled.

*   The HTML formatter must correctly process the closing angle bracket token of an inline element's closing tag even when that closing tag is the last token before a self-closing void element, without raising an 'unseen token' error.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.