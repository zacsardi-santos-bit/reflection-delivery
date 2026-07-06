## Description

The dirty fields tracking currently includes explicit "not dirty" markers for fields that haven't changed from their default values. When a form field's current value equals its default, it still appears in the dirty fields result — just with a marker indicating it is clean. This is confusing and creates clutter.

## Expected Behavior

- Fields that match their default values should be completely absent from the dirty fields result, not present with a "not dirty" marker.
- In array-based fields, positions where all values match defaults should appear as empty slots rather than objects filled with "not dirty" markers.
- When all values in a form match their defaults, the dirty fields result should be an empty object.
- If a nested structure would only contain "not dirty" entries after comparison, the entire nested structure should be pruned from the result.

## Why This Matters

The current behavior forces consumers to check the value of each dirty field key (is it marked as changed or unchanged?) rather than simply checking whether the key exists at all. A sparse representation — where presence means "dirty" and absence means "clean" — is more intuitive and makes it easier to work with dirty state in large forms, including those with field arrays.
