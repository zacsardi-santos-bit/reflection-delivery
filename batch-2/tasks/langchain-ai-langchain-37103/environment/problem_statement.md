## Description

Tool objects currently rebuild their input schema from scratch every time it is accessed, and the various schema-derived values (input validator, argument properties, character count) are each cached independently with no single source of truth. This leads to redundant work and makes cache invalidation fragile when a tool's name or description changes.

We need a unified, cached schema representation on each tool that is recomputed only when the tool definition changes. This object should provide:
- Input validation for tool call data
- A character count of the schema payload (for token estimation)
- Consistent invalidation when the tool's name or description is mutated

The new schema representation should also be exportable from the top-level tools module so callers can reference its type directly.

## Expected Behavior

- Accessing the tool's schema object multiple times returns the same cached instance
- Mutating the tool's name produces a fresh schema with the updated name on the next access
- The schema object validates input data and coerces it to the expected types
- The schema's character count matches what was previously reported by the approximate character count attribute on the tool itself
- The schema type is importable from the top-level tools namespace

## Related Issue

Additionally, when converting tools whose inputs include optional fields, optional fields are incorrectly included in the required-fields list. They should appear in the schema's properties but not be listed as required. Similarly, certain uncommon collection types (such as mutable sets) currently raise an error during schema conversion — these should be handled correctly.

## Why This Matters

Reducing redundant schema construction improves performance for applications that access tool schemas frequently. Correct optional-field handling prevents LLMs from being told a field is required when it is actually optional.
