## Description

The Divider component supports placing a text label inside a horizontal divider line, with options to align that label to the left, center, or right. When aligned to the left or right, a fixed default margin is always applied between the label and the nearest edge. There is currently no way to customize or override this default spacing.

Developers should be able to specify a custom margin between the divider label and its closest edge, overriding the default. When a custom margin is set, the component should visually reflect the absence of the default margin by applying appropriate indicator classes to the container element and applying the custom spacing value directly as an inline style on the label.

## Expected Behavior

- A new optional prop should allow developers to control the margin between the divider label and the left or right edge.
- Both string and numeric values should be accepted for the margin (e.g., a string value of zero for no margin, or a numeric value for a specific pixel distance).
- When a custom margin is provided for a left-oriented divider, a special CSS class should be added to the container signaling that the default left margin has been overridden, and the label's left margin should be set to the specified value.
- When a custom margin is provided for a right-oriented divider, a special CSS class should be added to the container signaling that the default right margin has been overridden, and the label's right margin should be set to the specified value.
- This prop should only take effect when the orientation is set to left or right.

## Why This Matters

Without this feature, developers cannot position a divider label flush against the edge or at any custom distance, limiting layout and design customization options.
