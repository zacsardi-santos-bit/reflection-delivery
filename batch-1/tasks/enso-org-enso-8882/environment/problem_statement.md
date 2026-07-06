## Description

The dashboard's JSON schema utilities are missing functions for working with schemas programmatically. Currently, there is no standard way to convert a known runtime value into its corresponding JSON schema representation, to check whether a value satisfies the constraints described by a schema, or to extract the single constant value that a constrained schema encodes.

## Expected Behavior

- A function that converts a constant value (such as a string, number, or integer) to an equivalent JSON schema should be available. If the conversion cannot be performed, it should return nothing.
- A function that checks whether a value matches a schema's constraints should be available. It must correctly handle type matching for strings, numbers, and integers; exact constant equality checks; and numeric "multiple of" constraints. An exact multiple of N must match; a non-integer multiple (e.g., N × 1.5) must not match.
- A function that extracts the constant value from a schema should be available. It should return the value as the first element of an array (tuple), allowing callers to retrieve the encoded constant.
- Converting a value to a schema and then extracting the constant from that schema should always recover the original value (round-trip property). The value should also match the schema it was converted from.

## Why This Matters

These utilities are foundational for features like dynamic form generation and configuration UIs, where the application needs to programmatically reason about what values a schema accepts, validate user input against schemas, and convert between runtime values and their schema equivalents. Without them, each feature must implement its own ad-hoc schema reasoning, leading to inconsistencies.
