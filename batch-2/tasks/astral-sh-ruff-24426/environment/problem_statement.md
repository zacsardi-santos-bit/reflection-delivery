## Description

The redundant dictionary key check lint rule does not correctly handle f-string expressions used as dictionary keys. When a developer writes a boolean condition that first tests whether an f-string key is present in a dictionary and then accesses the dictionary with that same f-string key, the rule should detect the membership test as redundant — but currently it either misses the pattern entirely or misclassifies the safety of the automatic fix.

## Expected Behavior

- When the f-string key contains a non-literal interpolated expression (such as a variable or object reference), the rule should flag the pattern but mark the automatic fix as **unsafe**, because evaluating the f-string a second time could invoke side-effectful methods (e.g., custom string conversion) and potentially produce a different result.
- When the f-string key contains only literal interpolations (like a numeric constant), the rule should flag the pattern and mark the fix as **safe**.
- When the key is a plain f-string with no interpolation at all, the rule should flag the pattern and mark the fix as **safe**.
- When the key expression involves a walrus operator (inline assignment), the rule must **not** emit any diagnostic, because auto-fixing would silently eliminate the assignment side effect.

## Why This Matters

F-strings are common in Python, and developers often use them to build dictionary keys. Without correct handling, the linter will silently miss redundant checks on f-string keys, or worse, suggest an unsafe automatic fix that could change program behavior by suppressing side effects. Consistent detection and correct safety classification gives developers accurate guidance and prevents subtly broken auto-fixes.
