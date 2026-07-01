## Description

The template language currently rejects any attempt to use a dynamic expression as the value of the style attribute on HTML elements. This means developers cannot pass variables, function calls, or computed values to the style attribute — they are forced to use hardcoded static strings only.

This restriction is unnecessarily limiting. Many real-world components need to apply styles that depend on runtime state: for example, a progress bar whose width is computed from a percentage value, a button whose border color reflects a validation error, or an element positioned based on user interaction. All of these require dynamic styles.

## Expected Behavior

- Developers should be able to pass Go expressions to the style attribute using the same brace syntax used for other attributes.
- The system should support a rich set of input types: plain CSS strings, pre-approved safe CSS values, maps of property names to values, conditional key-value pairs toggled by a boolean, zero-argument functions (with or without error returns), and slices of any of the above.
- Plain string values and property names should be automatically sanitized to prevent CSS injection attacks. Pre-approved safe CSS values should bypass sanitization but still apply HTML encoding.
- Map inputs should produce deterministically sorted output (alphabetical by property name).
- Invalid or unsafe property names and values should be replaced with well-known placeholder markers rather than passed through or silently dropped.
- If any input value is itself an error (or a function that returns an error), the error should be propagated to the caller.
- Inputs with unsupported types should produce a clearly recognizable placeholder value in the output rather than causing a crash.

## Why This Matters

Without this capability, developers are blocked from implementing common UI patterns like theme switching, form validation feedback, animated elements, or data-driven visualizations — all of which require runtime-computed styles. This change brings style attribute expressions into parity with the dynamic expression support already available for class names and other attributes.
