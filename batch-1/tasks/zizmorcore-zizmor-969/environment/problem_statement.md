## Description

The obfuscation detection for GitHub Actions workflows currently reports diagnostics that highlight the entire expression syntax — including surrounding delimiter characters — rather than the actual content that is obfuscated. This makes findings harder to read and less precise than they should be. The diagnostic messages are also verbose in ways that don't add clarity.

Additionally, there is a category of obfuscation that goes completely undetected: when a dynamic, computed value is used as an index key to access a collection. This pattern can hide which data is actually accessed at runtime and represents a form of obfuscation that security-conscious users care about.

## Expected Behavior

- For expressions that are fully constant and can be replaced by a static value, the diagnostic should highlight only the expression content itself, not the surrounding delimiter syntax. The report message should be shorter and focused.
- For expressions containing a constant-reducible subexpression, the diagnostic should point directly to that subexpression, not the entire enclosing expression. The report message should clearly indicate what can be reduced.
- When using the strictest analysis mode, the tool should detect and flag cases where a computed or dynamic value is used as an index key in an expression, reporting the finding at the location of the index access with a clear message.

## Why This Matters

Precise diagnostic spans make it immediately obvious what the user needs to change, without requiring them to parse the full expression structure themselves. Adding detection for computed index patterns closes a gap where dynamic lookups — which can obscure what data is being accessed — go unreported.
