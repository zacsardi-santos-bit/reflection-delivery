## Description

The formula engine is missing implementations for several common spreadsheet functions. Users trying to use standard spreadsheet formulas that check for blank cells, apply conditional logic, handle errors gracefully, combine boolean conditions, or concatenate text find that these operations don't work at all. In addition, when comparing values of different types (like a text value against a number, or a number against a boolean) in range-based conditions, the engine produces incorrect results because it doesn't apply the standard spreadsheet type-ordering rules.

## Expected Behavior

- A blank-check function should return true when a cell or value is empty and false for any non-empty value — including errors, booleans, strings, and numbers (even zero).
- A conditional branching function should return one value when a condition is true and another when it's false, supporting scalar and array inputs. When the condition or the return values are arrays, the result should expand to cover the full range; any out-of-bounds positions should return an appropriate "not available" error.
- An error-handling function should pass through a value unchanged when it's not an error, and substitute a fallback value when it is — including inside arrays, with array broadcasting support.
- An AND logic function should return true only if all provided logical values are true, false if any are false, and an error if no logical values can be found (e.g., all inputs are text). Error values in arrays should propagate.
- A text concatenation function should join multiple text values or arrays into a single string result, with proper handling of escaped quotation marks and array broadcasting.
- Cross-type comparisons must follow standard ordering: numbers are less than text, and text is less than booleans.

## Why This Matters

Without these functions, formulas that rely on any of these common operations fail entirely. These are foundational spreadsheet capabilities expected to work correctly in any formula engine.
