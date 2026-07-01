## Description

Pipeline conditions that compare numeric parameter values to integer literals currently fail with a type mismatch error. Numeric parameter values are stored internally as floating-point numbers, but users naturally write integer literals in their conditions (e.g., checking whether a counter equals 1 or a threshold equals 10). The expression evaluator does not support comparing these two numeric types directly, even though they represent the same number.

## Expected Behavior

- When a pipeline parameter has a numeric value (e.g., 1.0) and a condition compares it against an integer literal, the comparison should succeed and evaluate to true.
- Users should not be required to write explicit floating-point literals to avoid type errors when the parameter is stored as a floating-point value.
- The behavior should conform to the CEL language specification, which defines that numeric type promotion between integer and double is supported.

## Root Cause

The underlying expression evaluation library is at an older version that predates support for cross-type numeric comparison. Upgrading it to a more recent version enables the integer/double comparison overloads described in the CEL spec.

## Why This Matters

This bug forces pipeline authors to be aware of the internal storage type of their parameters and use non-intuitive notation when writing conditions, which leads to confusion and subtle mistakes. Fixing it brings the behavior in line with the language specification and user expectations.
