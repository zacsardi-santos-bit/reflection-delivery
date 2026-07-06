## Description

CSS codebases with properties listed in arbitrary order can be difficult to read and maintain. When reviewing style changes or debugging specificity issues, developers must mentally parse unordered property lists. It would be valuable to have a lint rule that detects when CSS properties within a rule block are not in a consistent, semantic order and offers an automatic fix.

## Expected Behavior

The linter should flag CSS rule blocks where properties are not in the expected semantic order and offer a safe auto-fix to reorder them. The intended order should group related concepts together:
- Custom properties (CSS variables) come first
- Layout properties come next
- Spacing (margin/padding) properties follow
- Typography properties (color, font) come after spacing
- Interactive behavior properties (pointer events, visibility) follow
- Decorative styles (borders, backgrounds) come next
- Transitions and animations come last
- Nested selectors and media queries are always placed at the end of their containing block

Additional behaviors:
- Vendor-prefixed variants of a property should be ordered before their non-prefixed counterpart
- Property name comparisons should be case-insensitive
- When a shorthand property legitimately follows its own longhand variant, no warning should be emitted (this is common when specifying overrides)
- The sort should be stable — duplicate property declarations preserve their relative order
- Nested rule contents should be independently checked and fixed as well

## Why This Matters

Consistent property ordering makes styles easier to scan, review, and maintain. Developers should be able to rely on a tool to enforce this ordering automatically rather than doing it by hand.
