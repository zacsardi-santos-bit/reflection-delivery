I'm working on the HTML parser in Biome and ran into a bug with how it handles closing tags on void elements.

*   When the HTML parser encounters a closing tag for a void element (such as a line break or input element), it must emit exactly one parse diagnostic per occurrence — not two or more.

*   The diagnostic message for a void element closing tag must be exactly: "Void elements should not have a closing tag. Remove the closing tag."

*   The diagnostic span for a void element closing tag must cover the entire closing tag from the opening angle bracket through the closing angle bracket (not just the tag name portion).

*   A closing tag for a void element must be represented in the AST as an HtmlBogusElement node containing the raw tokens (L_ANGLE, SLASH, HtmlTagName, R_ANGLE), rather than as a proper HtmlClosingElement.

*   A void element's closing tag must NOT be treated as a closing tag for any enclosing parent element. The parent element's own matching closing tag must still close it normally, and the document hierarchy must remain intact.

*   Void elements themselves (e.g., br, input) must continue to be parsed as HtmlSelfClosingElement nodes even when followed by a spurious closing tag.

*   The parser must correctly handle void closing tags that contain extra whitespace before the closing angle bracket (e.g., closing tags with trailing spaces before '>') and treat them the same as compact void closing tags.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.