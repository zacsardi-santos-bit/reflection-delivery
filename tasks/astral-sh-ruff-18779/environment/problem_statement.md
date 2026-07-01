## Description

The lint rule that enforces consistent parentheses style on pytest marker decorators gives incorrect results when a marker decorator appears in a file before the testing library is imported. Because the rule currently identifies markers through simple name pattern matching rather than verified import resolution, it can process decorators as if they were pytest marks even when the library hasn't been imported at that point in the file.

## Expected Behavior

- When a decorator that looks like a pytest mark appears before the relevant import statement, the rule should leave it alone — it cannot verify the decorator actually refers to the testing library's mark functionality.
- After the import is present, the rule should continue working as before, flagging any markers that use the wrong parentheses style.
- The rule should rely on the linter's semantic import-resolution system to confirm that a decorator refers to pytest marks before checking its parentheses style.

## Why This Matters

False positives reduce trust in linting output and force developers to add unnecessary suppression comments. By grounding the check in proper import resolution, the rule only reports violations it can actually verify, making it more accurate and reliable. This also aligns the rule with how the rest of the linter handles import-qualified names.
