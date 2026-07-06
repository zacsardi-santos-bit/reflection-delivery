## Description

The form label component does not adjust its text size based on its position within the form layout. When a label is placed to the left of its associated input field rather than above it, it should automatically use a slightly larger text size to maintain visual balance. Currently, however, the label always renders at the same size regardless of position.

Additionally, when a developer explicitly specifies their own font size for the label, those custom settings should always take precedence over any automatic sizing applied based on position. At the moment, position-derived font sizing is not being overridden properly when the developer passes their own typography settings.

## Expected Behavior

- When a label is positioned above the field (the default), it should render at the smaller standard text size.
- When a label is positioned to the left of the field, it should render at a slightly larger text size.
- When a developer explicitly provides a custom font size for the label, that size should always win over the automatic position-based size — even when the label is in the left position.

## Why This Matters

Without this behavior, left-positioned labels look visually inconsistent with the rest of the design system. And without respecting explicit typography overrides, developers lose the ability to customize label appearance in a predictable way.
