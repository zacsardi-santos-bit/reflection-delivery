## Description

The HTML analyzer is missing an accessibility rule that validates the correct usage of anchor elements in HTML-based template files. Anchor elements should only be used for navigation purposes with a real, valid destination. Currently, developers can write templates with anchors that have no destination, use invalid destinations (like JavaScript execution strings), or use anchors purely as interactive click targets — none of which are flagged by the linter.

## Expected Behavior

A new accessibility lint rule should flag the following patterns in HTML, Astro, Svelte, and Vue template files:

- An anchor element whose link destination is set to a JavaScript execution string — this is not a real URL and should be rejected with a message asking for a valid destination value.
- An anchor element whose destination attribute is present but has no value (empty/boolean form) — this should also be flagged as needing a valid destination.
- An anchor element that has a click event handler but no navigation destination — the developer should be told to use a button element instead, since the element is being used as an interactive control, not a link.
- An anchor element with no destination attribute at all — the developer should be prompted to always provide a navigation destination on anchors.

The rule should not flag:
- Anchors with valid URLs (absolute or fragment-based)
- Anchors with dynamic destination bindings (e.g., an expression as the destination)
- Non-anchor elements like named component wrappers, even if they have similar attributes

## Why This Matters

Misused anchor elements degrade accessibility for keyboard and screen reader users, who rely on anchors behaving as navigation links. Without this rule, developers may unintentionally produce markup that confuses assistive technologies. Enforcing correct anchor usage across all supported HTML-like template formats ensures consistent accessibility behavior.
