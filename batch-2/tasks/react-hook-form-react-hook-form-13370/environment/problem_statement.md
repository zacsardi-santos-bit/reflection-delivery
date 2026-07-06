## Description

There are two related bugs in how the library tracks which form fields are "dirty" (have changed from their default values).

**Bug 1: Array of objects loses per-property dirty granularity**

When a field holding an array of objects is updated programmatically with dirty tracking enabled, the library marks the entire array field as simply dirty (a flat boolean), instead of tracking which individual properties within each array item changed. The expected behavior is that dirty tracking should be granular — each property inside each array item should be individually flagged.

**Bug 2: Inconsistent dirty state when mixing dirty and non-dirty programmatic updates**

When a field's value is set programmatically without marking it as dirty, and later another field's dirty state changes (e.g., a user types into it and then reverts the value), the form can end up in an inconsistent state:

- The overall "is the form dirty" flag correctly reports the form as dirty (because a field still has a value different from the default)
- But the field-level dirty fields list is empty or incomplete — it doesn't show which specific field is actually dirty

After reverting the second field to its default, the specific field whose value differs from the default should appear in the dirty fields list.

## Expected Behavior

- Programmatic updates to array-of-objects fields with dirty tracking should produce per-property dirty state inside each array item
- When the form-level dirty flag and field-level dirty fields fall out of sync, the system should recompute dirty fields from scratch against default values
- Fields with values differing from defaults must always appear in the dirty fields list, even if they were set without explicitly enabling the dirty flag at that time

## Why This Matters

Forms that rely on the dirty fields state to show unsaved changes, conditionally enable save buttons, or drive validation logic will display incorrect information when these bugs occur. Users will either see too few dirty fields (inconsistency bug) or lose fine-grained information about which array item properties changed (granularity bug).
