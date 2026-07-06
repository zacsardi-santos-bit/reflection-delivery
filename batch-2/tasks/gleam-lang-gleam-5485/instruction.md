I'm working on the Gleam compiler and I'd like to add a new warning for a JavaScript-specific limitation in bit array pattern matching.

*   When compiling for the JavaScript target, if a bit array pattern contains an integer segment with a compile-time literal size greater than 52 bits (expressed either as a bare integer literal or via the size() option), the compiler must emit a warning.

*   The warning title for an oversized JavaScript bit array integer segment must be exactly: 'Truncated bit array segment'.

*   The warning body for an oversized JavaScript bit array integer segment must state the exact bit size and the JavaScript limit: 'This segment is a {N}-bit long integer, but on the JavaScript target numbers have at most 52 bits. It would be truncated to its first 52 bits.' where {N} is replaced with the actual size.

*   The warning hint for an oversized JavaScript bit array integer segment must be exactly: 'Did you mean to use the `bytes` segment option?'

*   The warning must only be emitted when the size is compile-time known and strictly greater than 52. Integer segments with size 52 or below must not trigger this warning.

*   The warning must only be emitted when the pattern segment binds a variable (e.g., `<<n:size(53)>>` or `<<number:123>>`); patterns that do not capture a value in the segment do not require this warning.

*   Existing warning messages for 'todo found', 'panic used as a function', and 'todo used as a function' must not have a blank line between the main warning body text and the 'Hint:' line — the hint must immediately follow the body text.

*   The 'Inefficient use of list.length' warning body text must be formatted as a single reflowed paragraph without a trailing blank line before the hint. The body must read: 'The `list.length` function has to iterate across the whole list to calculate the length, which is wasteful if you only need to know if the list is empty or not.'


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.