I'm working on a Card component in a React design system and need to make several improvements to its API.

The spacing for card sub-components (rows and footer) currently has to be passed down explicitly from the parent Card as a prop, which is error-prone. I'd like the parent Card to share its spacing setting automatically with sub-components through context, so that card rows and footer can consume spacing from context rather than receiving it as a direct prop.

The interactive card pattern also needs an overhaul. Right now there's a separate "interactive" flag and a custom action callback, but I want to replace this with a standard click handler prop. When that handler is provided, the inner content area of the card should render as a native button element so keyboard accessibility is handled by the browser. I'd also like to support a link destination prop that makes the card behave like an interactive link (pointer cursor, box shadow on hover and focus) the same way the click handler does.

When a card has a footer, the inner content area should only have its top corners rounded (not the bottom ones). Without a footer, all four corners should be rounded.

The card's sub-components — the column, row, and footer — should also start supporting data tagging attributes so they can be targeted in automated tests.

I need to rename the card width prop for clarity (a shorter, simpler name).

Finally, there should be a developer warning if someone passes a footer as a direct child of an interactive card (one with a click handler or link destination), as that's not a supported pattern.

As part of the same change, the Polish locale module needs to be moved into an internal subdirectory.
