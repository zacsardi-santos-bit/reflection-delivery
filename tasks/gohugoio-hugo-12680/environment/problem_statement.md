## Description

Hugo's built-in math template functions do not include trigonometric operations or angle conversion utilities. This makes it difficult for template authors who need to perform geometric calculations — such as generating SVG arcs, computing positions on a circle, or building angle-dependent visual layouts — to do so directly in templates without resorting to external tools or workarounds.

## Expected Behavior

The math namespace in Hugo templates should expose the following new functions:

- Access to the mathematical constant pi
- Sine, cosine, and tangent functions that accept radian inputs
- Inverse trigonometric functions: arcsine, arccosine, arctangent (both one- and two-argument forms)
- Utilities to convert between degrees and radians

All functions should accept any supported numeric input type. Functions that receive a non-numeric argument should return an error. For inputs that are mathematically valid but out of domain (such as computing arcsine of a value greater than 1), the functions should return a not-a-number result without raising an error.

## Why This Matters

Template authors working with SVG generation, circular layouts, map projections, and similar geometric use cases currently have no way to perform trigonometric calculations natively in Hugo templates. Adding these functions makes Hugo more capable for creative and data-driven template work without requiring custom shortcodes or external preprocessing.
