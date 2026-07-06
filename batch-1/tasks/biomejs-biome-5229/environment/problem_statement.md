## Description

The linter already detects unnecessary escape sequences inside regular expressions, but there is no equivalent check for string literals. Developers frequently write backslash sequences before characters that don't need escaping in the current string context — for instance, escaping a letter that has no special meaning, or escaping a quote character that is not the one delimiting the string. These superfluous backslashes are harmless at runtime, but they clutter the code and mislead readers into thinking a special character was intended.

## Expected Behavior

- A new lint warning should be emitted whenever a backslash is used before a character that does not need escaping in the given string literal (single-quoted, double-quoted, or untagged template literal).
- The warning should explain that only the enclosing quote character and recognized special sequences actually require escaping.
- An automatic safe fix should be available that removes the unnecessary backslash, leaving the character itself unchanged.
- Strings inside JSX attribute values and tagged template literals should be excluded from this check, as those contexts have different semantics.
- The existing similar check for regular expressions should use a consistent severity level — a warning rather than an error — so both checks are visually uniform in output.

## Why This Matters

Without this check, code reviews and automated tooling have no way to catch accidental or misleading escape sequences in strings. Adding this rule helps keep string literals clean, correct, and unambiguous, and the auto-fix makes it easy to remediate existing violations without manual effort.
