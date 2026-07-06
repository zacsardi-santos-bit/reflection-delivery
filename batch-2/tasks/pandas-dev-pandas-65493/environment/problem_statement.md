## Description

Pandas currently lacks proper support for complex number arrays within its extension array infrastructure and arithmetic system. When working with complex-typed data in Series, DataFrames, or Index objects, several issues arise.

## Current Behavior

- Arithmetic operations (addition, subtraction, multiplication, division) and mathematical functions (like square root) performed on complex-typed arrays incorrectly coerce the result dtype to a floating-point type instead of preserving the complex type.
- The extension array wrapper (which bridges plain numeric arrays into the extension array interface) has no test coverage for complex dtype, leaving behaviors like duplicate detection and item assignment untested and broken.
- Operations that are mathematically undefined for complex numbers (floor division and modulo) do not properly raise errors; this should produce a type error instead of silently succeeding or failing in unexpected ways.
- The CSV parsing engine for complex-typed extension arrays should be handled gracefully rather than failing unexpectedly.

## Expected Behavior

- Arithmetic on complex-typed arrays should preserve the complex dtype throughout.
- Duplicate detection on complex arrays should work correctly.
- Floor division and modulo on complex-typed arrays should raise an appropriate error.
- The extension array test suite should cover complex dtype, including known limitations (such as the absence of a low-level fill routine for complex types and CSV parsing with certain parser engines).

## Why This Matters

Users who work with complex-valued data (e.g., signal processing, scientific computing) expect pandas to correctly handle the complex dtype with the same level of support as integer and floating-point types, including correct dtype preservation through arithmetic and extension array operations.
