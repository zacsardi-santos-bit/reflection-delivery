## Description

When programmatically setting an array field to an empty array with dirty tracking enabled, the form does not correctly reflect that a change has been made. The dirty state remains false even though the array's contents have clearly changed from the default values.

## Steps to Reproduce

1. Create a form with default values containing an array of objects.
2. Register a child field within that array.
3. Subscribe to the form's dirty state and dirty fields map.
4. Programmatically set the array field to an empty array, passing the option to mark the field as dirty.
5. Observe that the form's dirty state is still false and the dirty fields map is empty — despite the array having been emptied.

## Expected Behavior

- The overall form should be marked as dirty.
- The dirty fields map should reflect the parent array field as dirty, not any individual child fields.

## Actual Behavior

- The form's dirty state remains false.
- The dirty fields map is empty, even though the value has been changed from its default.

## Why This Matters

This makes it impossible to reliably detect user-driven or programmatic changes to array fields when the new value is an empty array. Any logic that depends on dirty tracking (e.g., unsaved-changes warnings, conditional submit button enabling) will fail silently in this scenario.
