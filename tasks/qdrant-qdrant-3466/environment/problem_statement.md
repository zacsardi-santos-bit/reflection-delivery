## Description

Qdrant's filtering system currently supports three ways to combine conditions: all conditions must match, at least one condition must match, or conditions that must not match. However, there is no way to express "at least N out of M conditions must match" — a pattern that arises frequently in real-world use cases where rigid all-or-nothing filtering is too strict.

For example, a user may want to retrieve points that satisfy at least two out of three criteria (e.g., matching city, matching color, or having a specific count), without having to explicitly enumerate every valid combination. This is much more expressive than either requiring all conditions or requiring at least one.

## Expected Behavior

- A new filter clause should be supported that accepts a list of conditions and a minimum count threshold
- A point passes this filter if and only if it satisfies at least the specified number of conditions from the list
- This new clause should be composable with the existing filter types (all-must, at-least-one, must-not), both at the top level and nested within other conditions
- The new clause may also appear inside a "must not" context, which would exclude any point that satisfies the minimum threshold
- When the minimum count is not provided, the request should be rejected with a validation error (HTTP 400)
- Cardinality estimation for search query planning should correctly account for the new filter type

## Why This Matters

Users building complex filtering logic need a way to express threshold-based conditions without resorting to large, manually enumerated combinations of existing filter types. This addition makes the filter language more expressive and easier to use for a wide class of real-world scenarios.
