## Description

When computing spherical harmonics together with their first and second derivatives, the output arrays for the Jacobian and Hessian currently place the derivative dimensions as the **leading** axes. This is inconsistent with the standard array convention where batch/data dimensions come first and fixed-size structural dimensions come last.

For example, if you evaluate the spherical harmonic gradient over a batch of angular coordinates, the resulting array has the derivative components in the first positions rather than the last. This forces users to index into the front of the array to pick a derivative component, which breaks the usual ellipsis-based broadcasting idiom and makes the interface awkward compared to the rest of the library.

## Expected Behavior

- The Jacobian (first-derivative output) should place the 2-element derivative dimension as the **last axis** of the returned array, so that the theta-component and phi-component can each be selected via trailing-axis indexing across all batch dimensions.
- The Hessian (second-derivative output) should place the two derivative dimensions as the **last two axes**, so each second-derivative component can be retrieved via trailing two-axis indexing for all batch elements.

## Why This Matters

The current leading-axis convention makes it difficult to write generic, batch-friendly code when working with spherical harmonic derivatives. Moving derivative dimensions to trailing positions aligns the API with common array conventions (e.g., how matrix outputs from linear-algebra routines are shaped), improves usability with broadcasting and ellipsis indexing, and is consistent with how other derivative-returning functions in the scientific computing ecosystem are structured.
