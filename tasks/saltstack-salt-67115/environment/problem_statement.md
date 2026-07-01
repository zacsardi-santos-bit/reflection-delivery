## Description

The internal helper that determines whether a module function call succeeded is broken when the function's return value contains result information in certain dict structures. The helper currently requires two separate arguments — the raw return value and a separate changes dict — but this design means it cannot correctly inspect return values that embed result codes, numeric return codes, and nested state data all within a single dict.

## Expected Behavior

- The result-detection helper should accept a single return value and correctly determine success or failure from it
- When a function returns a dict containing a numeric return code, that code should influence the success determination (non-zero means failure)
- When both a result flag and a return code are present in the returned dict, a non-zero return code should override a positive result flag
- When the returned dict contains a nested sub-dict of state changes, that sub-dict should also be inspected for result and return code information
- Boolean return values should be passed through directly

## Why This Matters

When running module states that call functions which return structured result dicts (such as nested state executions), the actual result of the call may be incorrectly detected as successful even when sub-states failed. This causes Salt to report a false success state, masking real failures in module execution runs.
