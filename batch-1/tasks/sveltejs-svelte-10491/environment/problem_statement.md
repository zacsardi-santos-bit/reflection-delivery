## Description

The Svelte compiler's CSS scoping logic incorrectly determines which elements in a multi-part selector need the component-scoping attribute applied. This causes two opposite classes of bugs:

**Over-scoping with explicit combinators**: When a selector uses a direct parent-child or sibling relationship operator, the element on the "source" side of that operator is unnecessarily given the scoping attribute. This is redundant — the structural relationship already limits which elements the rule can match — and produces bloated, incorrect output.

**Under-scoping with descendant selectors**: When a selector uses the descendant relationship (one element anywhere inside another), only the last element in the chain is scoped. Intermediate elements in the chain are left without the scoping attribute, which can allow styles to leak beyond the component boundary.

## Expected Behavior

- Elements immediately before an explicit structural combinator (direct-child, adjacent-sibling, general-sibling) should NOT receive the scoping attribute in the generated CSS. Only the right-hand element of the combinator should be scoped.
- All intermediate elements in a descendant selector chain should receive the scoping attribute, and the corresponding HTML elements should also receive the scoping class in rendered output.
- Selectors whose descendant structure doesn't match any element in the template should be correctly identified as unused.
- Chains of multiple globally-declared selectors followed by a local element should work correctly with no false "unused selector" warnings.
- Native CSS nesting syntax should be supported, with nested rules correctly scoped and unused/empty nested rules annotated in comments.
- CSS attribute selectors containing special characters should be correctly parsed and preserved.

## Why This Matters

The incorrect scoping means component styles may either leak out of the component (due to under-scoping in descendant chains) or produce subtly wrong selector specificity (due to over-scoping with combinators). Both can cause hard-to-debug styling issues in real applications.
