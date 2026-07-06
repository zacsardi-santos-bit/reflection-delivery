## Description

The lint rule that simplifies verbose if-else assignment blocks into ternary expressions is unintentionally changing the quote style of f-strings when it applies its auto-fix. If a developer writes code where one branch of an if-else block assigns a single-quoted f-string, the tool's suggested fix converts those quotes to double quotes (or vice versa). This is a silent, unwanted code modification that overrides the developer's intentional style choice.

## Expected Behavior

- When an if-else block has an f-string using double quotes in one of its branches, the generated ternary expression should keep that f-string with double quotes.
- When an if-else block has an f-string using single quotes in one of its branches, the generated ternary expression should keep that f-string with single quotes.
- The rule should not alter f-string quote style in any way when producing a fix.

## Why This Matters

Developers rely on automated lint tools to improve code without introducing unintended changes. When a tool silently alters quote styles, it creates noisy diffs, may conflict with project-wide formatting conventions, and reduces trust in the tool. Preserving the original f-string quote style ensures that the auto-fix is a pure structural improvement with no unintended side effects.
