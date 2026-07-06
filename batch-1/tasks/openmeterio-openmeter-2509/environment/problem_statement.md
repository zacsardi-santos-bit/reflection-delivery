## Description

The filter types in our package don't have a way to check whether they are "empty" (i.e., have no conditions set). This makes it awkward for callers to determine whether a filter is a no-op before trying to apply it.

For example, when a filter is received as an optional query parameter, it may arrive as a zero-value struct with no conditions configured. Without an emptiness check, callers must inspect each individual field manually, which is error-prone and verbose.

## Expected Behavior

Each filter type (for strings, integers, floats, booleans, and time values) should expose a method to check whether it has any conditions set:

- A filter with no fields configured should report itself as empty.
- A filter with any field set — including comparison operators, logical operators (AND/OR), or pattern matchers — should report itself as non-empty.
- For boolean filters specifically, a filter explicitly set to a false boolean value must still report as non-empty, since a false value is a meaningful filter condition distinct from "no filter".

## Why This Matters

This allows callers to short-circuit processing when no filter criteria have been provided, making filter-handling code cleaner and less error-prone. It also avoids generating unnecessary query clauses when the user hasn't specified any filtering conditions.
