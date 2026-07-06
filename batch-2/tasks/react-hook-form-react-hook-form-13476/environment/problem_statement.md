## Description

When a field array uses custom validation logic that can produce both an array-level error (e.g., "at least N items required") and individual item-level errors (e.g., "this item's value is too short") at the same time, removing an item from the array causes the item-level errors to disappear — even after re-triggering validation.

## Steps to Reproduce

1. Set up a field array with custom validation logic that returns both a root-level error on the array (when the array has too few items) and nested errors on specific items (when an item's value fails some constraint).
2. Trigger validation — both error types are correctly shown.
3. Remove one of the items (which does not fix either the array-level or the item-level error), then re-trigger validation.
4. The array-level error is still shown, but the item-level errors for the remaining items are gone, even though they should still be present.

## Expected Behavior

- Both the array-level error and the per-item errors should remain visible after removing an item and re-validating, as long as the validation conditions still apply.
- If an item that had an error shifts to a new index after a removal, its error should appear at the updated index.

## Why This Matters

Developers who need to enforce both array-wide constraints (minimum length, etc.) and per-item constraints simultaneously will find that item-level validation feedback is silently lost after any removal, making it impossible to properly guide users through fixing all validation issues in a complex field array.
