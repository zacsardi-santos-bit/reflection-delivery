## Description

The repository does not have a factorial implementation that supports arbitrarily large numbers. Standard integer types can overflow when computing factorials for moderately large inputs, limiting the usefulness of any existing factorial utility. We need a factorial function backed by arbitrary-precision arithmetic so that results are always exact, no matter how large the input.

## Expected Behavior

- Computing the factorial of a non-negative integer should return the exact mathematical result as an arbitrary-precision unsigned integer.
- The factorial of 0 should return 1.
- The factorial of 1 should return 1.
- The factorial of 10 should return 3628800.
- The function should be available through the math module for use by other parts of the library.

## Why This Matters

Standard fixed-size integer types overflow quickly when computing factorials (e.g., 21! already exceeds the range of a 64-bit unsigned integer). Using arbitrary-precision arithmetic allows the function to handle all valid non-negative integer inputs and return mathematically correct results without overflow. This rounds out the math module with a useful and correct implementation that developers can rely on.
