## Description

The CSS formatter incorrectly moves inline comments in property declarations away from their original positions. Specifically, comments that appear between a property name and the colon, or between the colon and the value, are being relocated to appear before the entire property name. This changes the visual and semantic placement of the comment, breaking the developer's intent.

## Expected Behavior

- A comment written between a property name and the colon (directly before `:`) should remain in that position after formatting — it should appear after the property name but before the colon.
- A comment written between the colon and the value should remain immediately after the colon. To preserve readability, the value should then be placed on the next line with an extra level of indentation.
- If no comment appears between the colon and the value, the value should stay on the same line as the property without any extra line break.
- Comments written on their own line before a property declaration should remain before the property name, unaffected by these changes.
- These rules must also apply to declarations that appear inside at-rule feature queries.

## Why This Matters

CSS developers sometimes use inline comments to temporarily disable part of a value, to annotate why a specific value was chosen, or to preserve fallback values. When the formatter relocates these comments to before the property name, it breaks the intent of the comment and produces confusing output. The formatter should leave inline comments in their original logical positions within declarations.
