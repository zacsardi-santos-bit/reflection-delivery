## Description

When writing JSX code, it's easy to end up with inconsistent usage of curly braces — sometimes unnecessarily wrapping simple string values in expression containers, and other times forgetting to wrap expressions that require them. Currently there is no lint rule to enforce consistent curly brace usage across both JSX attribute values and JSX children.

## Expected Behavior

A new lint rule should be introduced to the nursery group that:

- Reports when a plain string value is wrapped in unnecessary curly braces as a JSX child (e.g., a string that could be written directly as text content)
- Reports when a plain string value is wrapped in unnecessary curly braces as a JSX attribute value (e.g., a string that could be a simpler attribute value)
- Reports when a JSX element used as an attribute value is not wrapped in curly braces (required for it to be valid)
- Provides automatic fix suggestions for all reported violations

The rule should NOT flag expressions that legitimately require curly braces (numeric values, variable references, etc.), nor should it flag code that is already correctly written. Edge cases like multiline string expressions and string expressions with surrounding inline comments should also be handled correctly.

## Why This Matters

Inconsistent use of curly braces makes JSX code harder to read and review. Unnecessary curly braces add visual noise, while missing required curly braces can cause confusing errors. An automated lint rule with auto-fix support makes it easy to keep JSX consistently formatted without manual effort.
