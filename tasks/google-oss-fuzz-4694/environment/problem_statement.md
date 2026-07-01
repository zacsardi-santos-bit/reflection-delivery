## Description

The compiler wrapper used in sanitizer library builds needs a utility function that strips certain linker flags that enforce strict symbol resolution. These flags — which cause linker errors when symbols are left undefined — are incompatible with sanitizer builds where some symbols may be intentionally unresolved.

The existing function needs to be renamed to follow standard Python naming conventions (lowercase with underscores). In addition, the function's logic needs to be corrected and extended to handle more flag formats:

- The standalone form of the "no undefined symbols" flag should be removed correctly, not just the two-token split form that passes the option through a separate argument.
- When the flag is split across two separate linker argument tokens and an unrelated argument appears between them, the function should still correctly identify and remove both tokens.

## Expected Behavior

- The function removes linker flags that enforce strict symbol resolution from compiler argument lists.
- Flags that are unrelated to symbol-definition enforcement (such as relro) are preserved.
- Compound linker arguments that mix relevant and irrelevant sub-options are handled correctly — only the relevant sub-options are removed.
- Split flag pairs (where the flag and its value appear in separate tokens) are removed even when other arguments appear between the two tokens.
- Multiple preceding arguments before the flag to be removed do not interfere with detection or removal.

## Why This Matters

Sanitizer builds often link against libraries that leave certain symbols unresolved intentionally. Passing strict "all symbols must be defined" linker flags causes these builds to fail. Stripping these flags from the argument list at wrapper level allows sanitizer instrumentation to proceed without requiring changes to each individual build system.
