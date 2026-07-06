## Description

The defined-name box logic in the spreadsheet UI — the code that decides what should happen when a user types in the name input and presses Enter — is currently deeply embedded inside a component, making it impossible to test in isolation. This logic covers several distinct cases: navigating to an existing named range, jumping to a typed cell or range reference, creating a brand-new named range, or rejecting invalid input. Because this behavior is untested, regressions are difficult to catch.

Similarly, the code that restores keyboard focus to the sheet grid after a defined-name action is confirmed has no unit coverage. It must handle two different scenarios: one where the inline cell editor is currently open (in which case the editor must be closed), and one where it is not (in which case various editor focus states must be reset and focus must return to the sheet).

## Expected Behavior

- When a user types a name that already exists (case-insensitive), the name box should navigate to that existing named range.
- When a user types a valid cell or range reference, the name box should navigate to that selection.
- When a user types a new valid name with no conflicts, it should trigger creation of a new named range.
- When a user types an invalid name (such as a name that conflicts with a sheet name), the input should be reset.
- Validation should return a clear duplicate-name error when a defined name with the same name already exists.
- After confirming a name, keyboard focus should be correctly restored to the sheet — either by closing the open cell editor, or by resetting all editor focus states.

## Why This Matters

Without unit tests for this logic, bugs in name-box behavior (wrong navigation, missed focus restoration, silent failures) go undetected. Extracting and testing this utility code makes the feature reliable and maintainable.
