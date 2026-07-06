## Description

The lint rule that suggests converting old-style type alias declarations to the modern Python syntax silently deletes comments when applying its automatic fix. This is a correctness issue: the fix modifies source code in a way that destroys user annotations without any warning.

## Expected Behavior

- If a comment exists within the type alias declaration in a location that will be removed by the fix, the fix should be marked as **unsafe** (requiring explicit user approval) rather than applied silently as a safe fix.
- If a comment appears only within the type value expression itself — the part that is carried over into the new syntax — the fix should remain **safe** and the comment should be preserved verbatim in the output.
- If a comment trails the end of the statement on the same line as the closing delimiter, and that comment is not within the replaced range, it should be preserved naturally without affecting the safety classification of the fix.

## Why This Matters

Users often include inline comments in type alias declarations to document constraints, workarounds, or other context. When the linter's auto-fix silently removes those comments, it corrupts the codebase without any indication. Users end up losing documentation they carefully wrote, and may not notice until reviewing a diff much later. The fix safety classification exists precisely to communicate this kind of risk — it should be used correctly here.
