## Description

When writing code that accesses tuple fields by numeric index inside certain macro calls, the rust-analyzer syntax highlighter incorrectly marks the field index as an unresolved reference. This causes valid code to appear visually broken in the editor — the numeric index shows up in an error color with a wavy underline, as if it were an undefined name.

## Expected Behavior

- Numeric tuple field accesses appearing inside macro arguments should be highlighted correctly as field accesses, not as unresolved references.
- The visual appearance in the editor should accurately reflect that the code is valid, with no false error indicators on tuple field indices.

## Current Behavior

When a tuple is created inline and its field is immediately accessed by a numeric index inside a macro argument (for example, passing such an expression to a format-string macro), the index number is rendered with the unresolved-reference highlight style. This is misleading — the code compiles fine, but the editor gives the impression something is wrong.

## Why This Matters

Developers relying on the editor's semantic highlighting for visual feedback get incorrect information. Tuple field access by numeric index is a common Rust pattern, and having those indices flagged as errors when they appear inside macros erodes trust in the editor's diagnostics and makes the development experience confusing.
