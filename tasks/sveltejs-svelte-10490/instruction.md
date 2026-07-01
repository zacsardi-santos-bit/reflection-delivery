Fix the CSS scoping logic in Svelte to handle various selector patterns correctly. Ensure that styles apply as intended and do not leak beyond component boundaries by addressing issues with descendant, child, and sibling combinators, as well as pseudo-class and nesting syntax.

*   Implement correct scoping for descendant combinators:
    *   Apply scoping attributes to every matched intermediate element in the chain.
    *   Use direct class selectors for the first matched element and `:where(.svelte-xyz)` for subsequent ones.

*   Correct scoping for child (`>`) and sibling (`+`, `~`) combinators:
    *   Do not apply `:where(.svelte-xyz)` to selectors before the combinator.
    *   Scope only the final target element.

*   Handle multi-level descendant selectors with missing intermediate elements:
    *   Identify and comment out unmatched selectors as unused in the compiled output.

*   Support and evaluate selectors using the `:is()` pseudo-class:
    *   Scope matching alternatives and comment out non-matching ones within `:is()`.

*   Implement support for native CSS nesting syntax:
    *   Treat nested rules as descendants and apply scoping modifiers appropriately.
    *   Comment out unused or empty nested rules.

*   Support explicit nesting selectors (`&`) in CSS:
    *   Resolve `&` correctly in relation to parent rule selectors.

*   Ensure correct compilation of chained global pseudo-class selectors:
    *   Preserve global segments without adding scoping classes.

*   Parse and scope CSS attribute selectors with special characters:
    *   Append `.svelte-xyz` to attribute selectors with special characters.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.