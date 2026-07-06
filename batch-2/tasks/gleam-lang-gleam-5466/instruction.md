I'm running into a frustrating issue where Gleam source files saved by certain editors (especially on Windows) fail to parse.

*   The Gleam parser must successfully parse expression-level source input that begins with the Unicode Byte Order Mark character (U+FEFF) — for example, a BOM immediately followed by valid Gleam code must parse without error.

*   The Gleam parser must successfully parse module-level source input that begins with the Unicode Byte Order Mark character (U+FEFF) — a BOM at the start of a module source file must be silently ignored, and the remainder of the module must parse normally.

*   When a BOM character appears at the very beginning of source input (either expression or module context), it must be consumed while preserving accurate byte positions. Since the UTF-8 encoding of the BOM occupies 3 bytes, subsequent tokens must have their source byte positions counted from the beginning of the original input — for example, a 4-character token immediately after the BOM must have start offset 3 and end offset 7.

*   The BOM fix must be implemented in the lexer (the component responsible for tokenizing source characters), so that token source spans in the resulting AST correctly reflect byte positions in the original source file.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.