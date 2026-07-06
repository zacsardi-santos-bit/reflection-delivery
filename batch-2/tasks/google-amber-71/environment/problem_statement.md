## Description

Several accessor methods in the codebase are using incorrect or imprecise numeric types, which causes compiler warnings (treated as errors on many configurations) and real precision loss at runtime.

Specifically:

- Some methods that logically deal with integer quantities — such as the stencil buffer clear value and the patch control point count — are returning or being compared as floating-point numbers instead of integers. This creates unnecessary type conversions and type-mismatch warnings.
- The conversion of large 64-bit integer token values to their double-precision representation is losing precision. When the minimum or maximum 64-bit signed integer value is converted and then retrieved as a double, the result does not accurately preserve the full double-precision value.
- Tolerance threshold values used in comparisons appear to be stored with insufficient precision (single rather than double), which can cause rounding errors in equality checks.
- Several single-precision float–valued accessors (for geometry coordinates, color components, depth/stencil values, and pipeline parameters) are being compared against double-precision literals, producing implicit-conversion warnings that fail strict builds.

## Expected Behavior

- Integer-valued accessors (stencil clear value, control point count) should return unsigned integer types.
- Single-precision float accessors should be cleanly comparable to single-precision float literals without implicit conversions.
- Converting a 64-bit integer token to double precision should preserve the full double-precision value, not silently round through an intermediate single-precision step.
- Tolerance values should be stored as double-precision to prevent rounding errors in comparison logic.

## Why This Matters

These type mismatches prevent the code from building cleanly under strict warning configurations and can introduce subtle numeric bugs (precision loss when handling edge-case integer values, or incorrect equality comparisons on tolerance thresholds).
