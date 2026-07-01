## Description

Biome is missing a lint rule to detect unnecessary escape sequences in JavaScript regular expressions. Developers often escape characters inside regex patterns that don't actually need escaping in their current context — for example, escaping a dot or question mark inside a character class, or escaping a hyphen at the very start of a character class where it would already be treated as a literal. These useless escapes add visual clutter and can mislead readers into thinking a character has special meaning when it doesn't.

## Expected Behavior

- A new lint rule in the nursery group should detect and flag unnecessary escape sequences in regular expressions.
- The rule should report a clear, actionable message indicating the escape is not needed.
- The rule should provide context-specific guidance explaining under what conditions that character *would* need to be escaped (e.g., "only outside a character class", "only in the middle of the class", "only as the first character").
- The rule should offer a safe automatic fix that removes the unnecessary backslash.
- The rule should handle nuanced context-sensitive cases: the position of a hyphen in a character class, the behavior of certain characters under the newer unicode mode flags, escape sequences like word-boundary assertions that only have meaning outside a character class, and named backreference syntax that is only meaningful in unicode-aware regular expressions.
- The rule should NOT flag escape sequences that are genuinely needed in the current position (e.g., a hyphen in the middle of a character class to denote a range, a caret as the first character of a class for negation).
- Unlike some comparable JavaScript linting tools, the rule should also report backreference-like escape sequences when used in a non-unicode-aware regex.

## Why This Matters

Unnecessary escapes in regular expressions are a common mistake that degrades code readability. Automating detection with precise contextual messages helps developers write cleaner, more understandable regex patterns.
