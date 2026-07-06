## Description

The auto-fix feature for rules that sort special Python collection variables can produce syntactically invalid code when the original collection has a trailing comma in an unusual position.

Currently, if a trailing comma appears directly before the closing bracket on the same line as the last element, or is placed on its own line (possibly separated from the surrounding items by blank lines or comments), the generated fix introduces a double comma — making the fixed file unparseable.

## Expected Behavior

The linter should:
- Correctly detect that the collection is unsorted in all of these trailing-comma variants
- Produce a valid auto-fix that sorts the elements without introducing a double comma
- Handle trailing commas that appear:
  - Directly before the closing bracket on the same line as the last element (e.g., the closing bracket follows the comma on the same line)
  - Before the closing bracket with extra surrounding whitespace
  - On a separate line, possibly surrounded by blank lines and comments
  - Interleaved with comments, where commas appear on their own lines between elements

## Why This Matters

Users who rely on the auto-fix to sort their collections will get broken code if the original definition uses any of these trailing-comma styles. This is a correctness bug: the linter claims to have fixed the code but leaves it in a non-parseable state. The issue affects sorting of both module-level export lists and class-level slot declarations.
