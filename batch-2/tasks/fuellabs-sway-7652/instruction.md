Fix the Sway code formatter to correctly handle the indentation of line comments within function bodies, ensuring that the formatter maintains idempotency and properly manages comments containing multi-byte Unicode characters.

*   Ensure the formatter is idempotent for function bodies in implementation blocks:
    *   When formatting already-correctly-indented code, the output must be identical to the input.
*   Maintain the indentation level of line comments between statements in an `impl` function body:
    *   Comments must not be shifted, and subsequent lines must retain their correct indentation.
*   Preserve the correct indentation for line comments appearing after block expressions within function bodies:
    *   Ensure comments following constructs like `if/else` branches maintain statement-level indentation.
*   Correctly handle comments with multi-byte Unicode characters:
    *   Use the byte length of inserted comment text as the offset for subsequent insertions, not the Unicode character count.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.