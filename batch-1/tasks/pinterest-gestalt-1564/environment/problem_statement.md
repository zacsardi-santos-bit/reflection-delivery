## Description

The Button component has a prop that controls whether it renders inline or full-width, but the prop's name and default value are confusing. Currently, the prop is named in a way that describes the "inline" state, meaning the absence of the prop (i.e., the default) actually results in a full-width button — the opposite of what the prop name implies. This has led to widespread misuse and boilerplate in the codebase where the prop is explicitly set just to get the non-default behavior.

We want to rename this prop to clearly communicate what it enables — full-width rendering — so that the default (no prop) gives you an inline button, and you only add the prop when you explicitly want a full-width button.

## Expected Behavior

- The Button component should accept a full-width boolean prop (defaulting to false) that causes the button to expand to fill its container when enabled.
- Without the full-width prop, buttons render inline (sized to their content).
- The old prop should be removed.
- Similarly, the internal link wrapper component should be updated to use the full-width prop instead of the old inline prop to control layout, with corresponding inverted semantics.
- A codemod should be provided to automatically migrate existing code: buttons that currently have no inline-related prop should gain the full-width prop (to preserve their current full-width rendering), and buttons that explicitly used the old prop to opt into inline behavior can have that prop removed.

## Why This Matters

Without this change, developers have to think backwards when writing button code — setting a prop called "inline" to false to get full-width behavior, or knowing that the absence of the prop means full-width (not inline). Renaming to the full-width prop makes the API self-documenting and prevents this confusion. The included migration tool ensures no regressions in existing codebases.
