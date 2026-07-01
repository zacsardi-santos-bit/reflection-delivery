## Description

The experimental icon package currently lacks a component for rendering icons from custom or built-in fonts. Many icon libraries distribute their icons as font files, where each icon is a specific character at a known Unicode code point. Without a dedicated font icon component, developers using this design system cannot display font-based icons in a consistent, reusable way.

## Expected Behavior

- A new font icon component should be available in the experimental icon package.
- The component should accept a font family name, a numeric character code (codepoint), a display color, and an optional font size.
- When rendered, the component should display the character corresponding to the given codepoint, using the specified font family and color.
- The component should render as a plain text element (no extra wrapping) so it integrates smoothly with existing layouts.
- The component should remain stable across multiple renders when given the same inputs — it should not produce inconsistent or flickering output.

## Why This Matters

Developers working with icon fonts need a standardized way to render these icons within the design system. Without this component, they must either write custom one-off solutions or use workarounds that are inconsistent with the rest of the component library. Adding this component fills that gap and enables font-based icon rendering as a first-class citizen in the experimental icon package.
