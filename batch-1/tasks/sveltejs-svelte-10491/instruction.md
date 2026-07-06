Implement the correct scoping logic for the Svelte compiler's CSS processing to address issues with over-scoping and under-scoping in selectors. Ensure that the scoping attribute is applied accurately based on the type of combinator used and the structure of the CSS selectors.

*   Correctly handle explicit combinators in CSS selectors:
    *   Do not apply the scoping attribute to elements immediately to the left of child (>) or sibling (+, ~) combinators. Only scope the right-hand element.
    *   Preserve universal selectors (*) before explicit combinators without wrapping them in scoping attributes.

*   Ensure proper scoping for descendant selectors:
    *   Apply the scoping attribute to all intermediate elements in a descendant selector chain, not just the last element.
    *   Reflect these scoping attributes in the corresponding HTML elements in the rendered output.

*   Identify and handle unused selectors:
    *   Mark descendant selectors whose intermediate ancestor elements are absent from the component template as unused, and wrap them in comments: /* (unused) <original selector> { ... }*/.
    *   Ensure chains of :global() pseudo-selectors followed by a local element are processed without emitting unused-selector warnings.

*   Support native CSS nesting syntax:
    *   Apply scoping to used nested selectors.
    *   Wrap unused nested selectors in /* (unused) ... */ comments.
    *   Wrap nested rules with no matching content in /* (empty) ... */ comments.

*   Correctly parse and preserve CSS attribute selectors:
    *   Ensure values containing special characters (such as {, }, ;, [, ]) are not corrupted in the scoped output.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.