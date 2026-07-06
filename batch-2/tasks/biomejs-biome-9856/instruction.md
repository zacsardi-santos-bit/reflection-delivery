I'm working on the markdown formatter and noticed it's handling hard line breaks incorrectly in a specific case.

*   When a hard line break (two trailing spaces) appears on an interior line of a paragraph — meaning there is at least one more content line following in the same block — the two-space marker must be preserved in the formatted output.

*   When a hard line break (two trailing spaces) appears on the last line of a paragraph block — meaning no subsequent content line follows in the same paragraph — the trailing two-space marker must be removed from the formatted output. The line itself still ends with a newline.

*   The formatter must produce output matching the reference formatter for a single-line paragraph ending with trailing spaces followed by an empty line: the trailing spaces are stripped.

*   The formatter must produce output matching the reference formatter for a multi-line paragraph where the last line has trailing spaces followed by an empty line: the trailing spaces are stripped from the last line only, while interior lines with trailing spaces are preserved.

*   When a block containing hard line breaks is followed by an empty line and then another block, the formatted output must correctly handle the hard line breaks within each block and the separator between them.


*   Interface details: Type: Function/Rule Implementation
Name: FormatMdHardLine (format rule for hard line break nodes)
Location: crates/biome_markdown_formatter/src/markdown/auxiliary/hard_line.rs
Description: Implements formatting for markdown hard line break nodes. The formatter must detect whether a hard line break is the last one in its paragraph block (i.e., has no subsequent content sibling, or the next sibling is empty text). If it is the last hard line break, the two-space trailing marker must be removed from output while still emitting a newline. If it is not the last hard line break, the two-space marker must be preserved as-is.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.