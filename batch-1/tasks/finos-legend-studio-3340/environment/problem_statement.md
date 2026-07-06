## Description

The query builder's filter panel currently only supports comparing a model property against a literal value (e.g., comparing one property against a literal string value). There is no way to compare one property against another property — for example, filtering where one property path equals another property path. Users should be able to drag properties from the model explorer or columns from the fetch structure panel directly onto an existing filter condition to use another property as the right-hand side of the comparison.

## Expected Behavior

- Dragging a compatible property from the explorer or a compatible column from the fetch structure panel onto an existing filter condition should set that property/column as the right-hand side value of the condition.
- A drop zone indicator should appear only when the dragged property is type-compatible with the filter condition's left-hand side.
- A reset button should allow removing a property-based filter value.
- Attempting to use a derivation column (either as a new filter condition or as a filter value) should be rejected with a clear warning message.
- Attempting to use a collection-type property, or a property expression inside a collection filter, as a filter condition value should also be rejected with appropriate error messages.
- When a derived property requiring arguments is used on both sides of a filter condition, each side must independently track its own argument values — setting one should not affect the other.
- Switching the filter operator on a condition with an empty string value to a list-based operator should cleanly convert the value to an empty list.

## Why This Matters

Without the ability to compare two model properties against each other in a filter, users are limited to literal comparisons only, which prevents many useful queries from being expressed in the UI. Clear validation messages and independent argument tracking ensure a reliable and predictable experience when building complex filter conditions.
