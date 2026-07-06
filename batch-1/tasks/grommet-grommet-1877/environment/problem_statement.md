## Description

The Box layout component currently has no built-in way to control spacing between its child elements. When developers want to add gaps between children in a row or column layout, they must manually apply margins or padding to each individual child, which is repetitive and breaks consistency with the rest of the design system's spacing scale.

## Expected Behavior

- The Box component should accept a new spacing property that allows developers to declare how much space should appear between its children.
- The spacing should use the same size tokens available throughout the design system (extra small, small, medium, large, and extra large).
- The spacing should be applied between adjacent children — not before the first child or after the last one.
- The available values and a description of the property should be reflected in the component's documentation.

## Why This Matters

Without a first-class gap option, achieving consistent inter-child spacing requires boilerplate per-child styling. A dedicated spacing property on the container keeps layout intent readable in a single place and ensures spacing values stay aligned with the design system's standard scale.
