## Description

When using role-based locators to find elements on a page, it's currently only possible to filter by accessible role and accessible name. However, many modern UIs have multiple elements with the same role and the same name — for example, several alert banners all labeled "Upload successful" but each describing a different file. In these situations, there is no accessible way to distinguish between them, forcing developers to fall back to brittle CSS or XPath selectors.

## Expected Behavior

Developers should be able to additionally filter role-based locators by the element's **accessible description** — the supplementary text associated with an element that screen readers announce alongside the element's name. This should support:

- **Substring matching** by default (case-insensitive)
- **Exact matching** (case-sensitive, full string) when explicitly requested
- **Regular expression matching**
- Resolving the description from multiple sources: a direct description attribute, text content of referenced elements, and a title attribute as a fallback
- Whitespace normalization so minor whitespace differences don't prevent a match

The description filter should work alongside the existing name filter, and the exact matching flag should apply to both when combined.

## Selector Generation

Playwright's automatic selector generator should also be aware of accessible descriptions. When the accessible name alone is not enough to uniquely identify an element (because multiple elements share the same name and role), the description should be automatically included in the generated selector. When the name is already unique, the description should be omitted. If both name and description are non-unique, the generator should fall back to a position-based selector.

## Why This Matters

This makes it possible to write robust, accessibility-oriented tests for pages where multiple elements share the same role and visible name, which is common in notification-heavy UIs, file-upload confirmations, and list views with repeated action buttons.
