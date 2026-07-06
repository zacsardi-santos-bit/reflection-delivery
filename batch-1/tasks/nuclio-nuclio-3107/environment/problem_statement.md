## Description

There is a bug in the utility function used to populate fields from a map of default values. The function is supposed to fill in only the fields that are empty or unset, leaving already-populated fields untouched. However, the current implementation incorrectly overwrites existing field values when a new value is provided, even if the field has already been set to something meaningful.

## Expected Behavior

- If a field already has a non-zero value (e.g., a non-empty string, a non-zero integer, or a boolean set to true), the function should leave it unchanged.
- Only fields that are at their zero/empty state should receive the provided default value.
- This behavior should apply consistently for string, integer, and boolean field types.

## Current Behavior

The function currently only checks whether the **incoming value** is non-zero before assigning it. It does not check whether the **existing field value** is already set. As a result, fields that have already been given a value may be overwritten unintentionally.

## Why This Matters

This bug can cause configuration or object state to be incorrectly overridden with defaults, even when the caller has explicitly set a field. Any code that relies on this function to safely apply defaults without clobbering existing values will silently behave incorrectly.
