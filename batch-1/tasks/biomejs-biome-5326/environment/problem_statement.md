## Description

The exhaustive-dependencies lint rule flags React hooks whose dependency arrays are either missing required entries or contain unnecessary/duplicate ones. However, the rule currently only reports the problem — it provides no automatic fix suggestion. Developers have to locate and edit every flagged dependency array by hand.

## Expected Behavior

- When a hook's dependency array is missing one or more values that are used inside the hook, the lint diagnostic should offer an automatic unsafe fix that inserts the missing entries.
- When a hook's dependency array contains unnecessary or duplicate entries, the lint diagnostic should offer an automatic unsafe fix that removes them.
- The diagnostic header for the rule should indicate that an auto-fix is available.

## Additional Fix-Validation Issue

A secondary problem occurs when the linter tries to validate a code action on source code that already has syntax errors. In that case the linter panics unnecessarily, because it expects the modified code to be valid even though the original was already malformed. The validation should be skipped when the original source is already invalid.

## Why This Matters

Auto-fix support dramatically speeds up addressing hook dependency warnings — especially in large codebases — by letting developers accept suggested changes with a single action instead of editing every site manually. Fixing the bogus-node panic also prevents false failures on test files that deliberately contain malformed syntax.
