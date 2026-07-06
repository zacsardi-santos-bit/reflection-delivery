## Description

The Card component family needs several improvements to better align with the design system and improve developer ergonomics. Currently, the spacing for card rows and footer sub-components must be passed down as an explicit prop from the parent Card — there is no automatic sharing of the spacing value. Additionally, the interactive card behavior is configured through a combination of an "interactive" flag and a separate action callback, which is non-standard and more complex than necessary.

## Expected Behavior

- Spacing should be shared automatically from Card to its sub-components (CardRow and CardFooter) via an internal context, without requiring explicit prop passing.
- Card should accept a standard click handler prop instead of the current "interactive flag + action callback" pattern. When a click handler is provided, the card's inner content area should render as a native button element.
- Card should also support a link destination prop for link-style interactivity, triggering the same pointer cursor and hover/focus shadow effects as the click handler.
- When a footer element is present alongside the card content, only the top corners of the content area should be rounded (bottom corners remain square). Without a footer, all corners are rounded.
- The CardColumn, CardFooter, and CardRow sub-components should support data tagging attributes for better test automation.
- The card width prop should be renamed for clarity.
- A developer warning should be issued when a footer is passed as a direct child element while the card also has a click handler or link destination prop, as this is an unsupported combination.
- The Polish locale module should be reorganized into an internal directory.

## Why This Matters

These changes make the Card API more consistent with standard HTML patterns, reduce the amount of manual prop wiring developers must do, and improve tagging support for automated testing. The interactive card model becomes simpler to use and more accessible by relying on native browser behavior.
