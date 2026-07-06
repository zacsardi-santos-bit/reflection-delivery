I'm working on improving the markdown formatter and I've noticed a few issues with how fenced code blocks are handled.

*   The markdown formatter must normalize all fenced code block delimiters to backtick style (triple backtick or longer), regardless of whether the original source used backtick or tilde (e.g. ~~~ or ~~~~) delimiters.

*   When computing the minimum fence length needed to avoid ambiguity with content inside a fenced code block, only backtick character sequences within the content must be counted (not tilde sequences), since the output always uses backticks.

*   The formatter must trim any trailing whitespace from code fence info strings (language/metadata tags following the opening fence characters).

*   The formatter must not insert any space between the opening fence characters and the info string — the info string must appear immediately after the last fence character.

*   The formatter must correctly handle and preserve code fence info strings that contain commas or multiple space-separated values (e.g. 'rust,ignore' or 'rust,  ignore   expect_diagnostics'), keeping their internal structure intact.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.