## Description

The linter incorrectly flags certain f-string literals inside union type annotations as duplicates when they are actually distinct. This happens with f-strings that use the self-documenting debug format specifier, where the surrounding whitespace or the source text of the expression itself becomes part of the runtime output.

For example, two f-strings that differ only in the whitespace around the expression name in the debug format, or that use different notations for the same numeric value, should be treated as different literal type members — but currently the linter flags them as redundant duplicates.

At the same time, the parser's abstract syntax tree representation of debug f-string expressions is missing the source text of the expression itself. The AST node currently stores only the surrounding whitespace/text on either side of the expression, but not the expression's own source text. This makes it impossible for downstream tools to correctly reconstruct or compare the full debug text using only the AST.

## Expected Behavior

- When comparing two literal f-string members in a union type annotation, the comparison must account for the full source text of any debug format specifier, including the expression text itself.
- Two f-strings with the same expression but different whitespace around the debug specifier must be treated as distinct (they produce different runtime output).
- Two f-strings with the same expression in different numeric notations (e.g., hex vs. octal) must be treated as distinct.
- True duplicates (f-strings with identical source text) must still be flagged.
- The AST node for debug f-string expressions must include the expression's own source text as a separate field, accessible alongside the leading and trailing text portions.

## Why This Matters

Without this fix, the linter produces false positives on valid code and may also incorrectly auto-fix (remove) one of the union members, changing the type semantics. Tools that consume the AST also lack access to the expression source text, making it harder to perform accurate analysis or formatting of debug f-strings.
