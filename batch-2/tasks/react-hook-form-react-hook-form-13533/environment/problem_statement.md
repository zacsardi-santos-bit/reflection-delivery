## Description

There is a bug in the deep equality utility where an empty array is incorrectly considered equal to an empty plain object. Because both structures have zero properties/elements, the comparison logic treats them as equivalent — but they are fundamentally different types and should never be considered equal.

## Expected Behavior

- Comparing an empty array with an empty plain object should be considered not equal, regardless of argument order.
- When two objects share the same keys but one key holds an empty array and the other holds an empty plain object, the objects should be considered not equal.

## Why This Matters

This bug can cause incorrect change-detection behavior in form state management. If a field value transitions from an empty array to an empty object (or vice versa), the change should be detected. With this bug, such transitions are silently ignored, leading to subtle form state inconsistencies where the UI does not reflect the actual data structure change.
