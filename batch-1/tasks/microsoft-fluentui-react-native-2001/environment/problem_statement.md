## Description

The Floating Action Button component on iOS always renders with a shadow effect, even when a developer tries to disable it by customizing the shadow setting to an absent value. Instead of truly removing the shadow, the component falls back to rendering a zero-opacity shadow object — meaning the shadow wrapper view is still present in the rendered tree. There should be a clean way to opt out of shadow rendering entirely.

## Expected Behavior

- Developers should be able to create a variant of the button with no shadow by setting the shadow styling token to an absent value during customization.
- When the shadow token is absent, the component should render without any extra shadow wrapper view — just the core button element.
- The shadow-free variant should render identically to a normal button in every other respect: accessibility attributes, interactive handlers, and all visual styles remain in place.

## Why This Matters

In certain contexts (such as notification bars or flat-layout surfaces), shadows should not appear on the button. Without this fix, developers are forced to work around the issue using empty shadow values, which still pollute the rendered tree with an unnecessary wrapper view. Properly supporting an absent shadow token keeps the component tree clean and aligns the token system with expected behavior.
