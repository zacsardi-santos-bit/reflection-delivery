I'm running into a bug in the SWC HTML toolchain when processing invalid but browser-recoverable HTML.

*   When the HTML code generator processes a document fragment containing a block-level element directly nested inside an inline element (such as a paragraph element inside a span element), the regular output must wrap the content in a full HTML document structure (html, head, body tags) while preserving the original nested element structure inside body.

*   When the HTML code generator processes a document fragment containing a block-level element directly nested inside an inline element, the minified output must preserve the original source markup exactly, without altering the nesting structure.

*   When the HTML minifier processes a document fragment containing a block-level element directly nested inside an inline element (error recovery scenario), the minified output must preserve the original element nesting structure exactly, without altering or collapsing it.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.