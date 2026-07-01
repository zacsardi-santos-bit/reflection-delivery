## Description

Cedar policy language should support checking whether a chain of nested optional attributes all exist in a single existence check expression, rather than requiring separate checks for each level. When writing policies that access deeply nested optional attributes, users should be able to write a single existence check covering the entire attribute path, and the system should correctly treat that as a complete guard for the whole chain.

## Expected Behavior

- A single existence check on a multi-level attribute path should serve as a valid guard for accessing that full path downstream in the same policy condition.
- The type checker should accept policies where the guarded path exactly matches the subsequent access.
- The type checker should still reject policies where the access goes deeper than what was guarded, or where the access uses a different attribute at the same level than what was actually checked.
- When rejected, errors should be precise: pointing to the unsafe access expression and including contextual information about what was actually guarded.
- The policy formatter should correctly lay out multi-level existence check expressions — compact and inline when they're simple, properly expanded with per-segment indentation when comments are present, and wrapped appropriately when attribute names are long.
- When a reserved keyword is mistakenly used as an attribute name in an existence check, the parse error should point to just that reserved keyword, not the whole surrounding expression, and the error message should identify it as a reserved identifier.

## Why This Matters

Without this feature, users either need cumbersome chains of separate existence checks to safely access nested optional attributes, or they are denied a cleaner, more expressive syntax that the policy language should be able to support. The type checker needs to correctly understand the semantics of multi-level existence checks to give useful, accurate feedback on policies that use this syntax.
