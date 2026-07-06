## Description

When appending or removing items from a dynamic field list, the form incorrectly marks unrelated fields as "dirty" (modified), even though the user never touched those fields.

For example, consider a form with a "name" text input, an "age" number input, and a dynamic list of items. If a user only clicks "Add Item" to append a new entry to the list, the dirty fields tracker should only show the list itself as modified. Instead, it incorrectly includes "name" and "age" in the set of dirty fields — as if those were changed — even though the user did nothing to them.

## Expected Behavior

- Appending an item to a field list should mark only that list as dirty.
- Removing an item from a field list should mark only that list as dirty.
- Unrelated scalar form fields that the user never modified should never appear in the dirty fields collection as a result of field list operations.
- When a user modifies a scalar field AND performs a list operation, both should appear as dirty — but only those two, not everything else.
- Forms with multiple independent dynamic lists should track dirty state per-list: appending to one list should not make the other list appear dirty.
- In forms with nested dynamic lists (a list nested inside another at a specific index), appending a new item should correctly update only the relevant branch of dirty state, with pre-existing items marked as not dirty and newly added items marked as dirty.
- Dirty state for scalar fields must be preserved across subsequent list add/remove operations.

## Why This Matters

Applications commonly use dirty field state to control UI behavior — such as enabling a "Save" button only when something has genuinely changed, or sending only the modified fields to the server. The current bug causes false positives, leading to unintended form submissions or incorrect "unsaved changes" warnings for fields the user never interacted with.
