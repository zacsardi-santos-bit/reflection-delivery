## Description

The gateway codebase has logic for checking whether a set of label keys all exist in a label map, but this logic is currently implemented as private helper functions within a single package. As a result, other packages cannot reuse this functionality and would need to duplicate the code if they required the same behavior.

## Expected Behavior

A shared, public utility function should be added to the provider utilities package that:
- Accepts a label map and a list of label keys to check
- Returns true when all specified label keys are present in the map
- Returns false when any specified label key is absent from the map
- Returns false when the label map is empty but label keys to check are provided
- Returns true when the list of label keys to check is empty (no conditions to satisfy)

## Why This Matters

Having this logic centralized in the shared utilities package means it can be used consistently across multiple parts of the gateway provider. This avoids code duplication, improves testability, and ensures that any future changes to label-matching logic only need to happen in one place. The existing private implementation can then be replaced with a call to this shared utility.
