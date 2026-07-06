## Description

The lint rule that checks for mismatches between the number of format placeholders and substitutions in percent-style format strings does not currently handle the case where the format string has **zero** placeholders but the expression still passes a non-empty value on the right-hand side.

If a plain string with no format placeholders is used with the percent format operator and a value is supplied on the right-hand side, that value will be silently discarded at runtime. This is almost certainly a bug, but the rule currently stays silent about it.

## Expected Behavior

When a percent-style format string has no format placeholders, the rule should report a diagnostic for any right-hand side that is not an empty tuple. This includes:

- Literal values (numbers, non-empty tuples)
- Variable names, including undeclared/unknown variables
- Function call return values
- Attribute access expressions

The only accepted exception is an empty tuple as the right-hand side, which is a valid Python pattern when no substitution is intended.

## Why This Matters

Silent, incorrect use of the percent format operator in cases where the format string has no placeholders is very unlikely to be intentional. Flagging these cases helps developers catch accidental formatting code, typos where a placeholder was forgotten, or leftover expressions from refactoring.
