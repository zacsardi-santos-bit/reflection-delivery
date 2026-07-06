## Description

Svelte's CSS scoping system produces incorrect output for several common selector patterns, causing styles to either fail to apply or leak beyond the component boundary.

## Problems

**Descendant combinator scoping is wrong**: When a rule uses multi-level descendant selectors (e.g., styling deeply nested elements), intermediate elements in the chain are either skipping the scoping attribute they should receive, or the wrong intermediate elements are being scoped. The result is that styles don't apply correctly.

**Child and sibling combinator selectors are over-scoped**: Selectors that appear *before* child (`>`), adjacent sibling (`+`), and general sibling (`~`) combinators are being given unnecessary scoping modifiers. Only the final target element of such a selector should be scoped, not the preceding context selectors.

**Selector-grouping pseudo-class is unsupported**: Using the selector-grouping pseudo-class in component styles doesn't work at all. The compiler can't tell which alternatives inside it match template elements, so none are scoped and none are correctly identified as unused.

**CSS nesting syntax is unsupported**: Modern native CSS nesting — both the implicit parent-reference form and the explicit nesting selector — is not handled by the compiler. Writing nested rules in a Svelte component's style block produces broken output.

**Special characters in attribute selectors cause parsing errors**: Attribute selectors whose values contain characters like braces, brackets, and semicolons fail to parse correctly.

## Expected Behavior

- Multi-level descendant selectors should scope every matched intermediate element in the chain
- Selectors preceding child or sibling combinators should not receive scoping modifiers
- Selectors inside the selector-grouping pseudo-class should be individually evaluated: matching ones get scoped, non-matching ones are marked as unused
- Nested CSS rules (both implicit parent-reference and explicit nesting selector forms) should be correctly analyzed and scoped
- Attribute selectors with special characters in values should parse and scope correctly
- Completely unmatched multi-level descendant selectors should be identified as unused

## Why This Matters

These are all valid, everyday CSS patterns. Developers writing natural CSS in their Svelte components shouldn't have to work around the scoping compiler's limitations. The broken intermediate-element scoping is especially problematic because it silently produces CSS that doesn't match the intended specificity or element set.
