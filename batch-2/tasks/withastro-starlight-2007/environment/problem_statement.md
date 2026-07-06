## Description

The Starlight Tailwind integration is generating outdated CSS when dark mode variant utility classes are used. The selector pattern for dark mode variants is structured so that the utility class is nested inside the dark-theme scope condition, but the current version of the underlying Tailwind CSS library produces a different structure — the class selector appears first, followed by the theme scope condition appended to it.

Additionally, the base CSS layer is missing several CSS custom properties for layout containment that newer versions of the Tailwind CSS library include automatically. These four layout containment custom properties must appear in both the universal selector block and the backdrop pseudo-element block of the generated base CSS.

## Expected Behavior

- When dark mode variant utility classes are compiled, the resulting CSS selector should place the utility class first, then append the dark-theme ancestry condition using a conditional pseudo-class with a wildcard descendant selector — rather than nesting the class inside the theme condition.
- The generated base CSS layer should include the four layout containment CSS custom properties, each initialized to an empty string, in both the universal and backdrop pseudo-element selector blocks.

## Why This Matters

Developers using Starlight's Tailwind integration rely on dark mode classes working correctly. If the generated CSS selector structure doesn't match what the current Tailwind version produces, styles may not apply as expected or may conflict with other styles. Keeping the integration in sync with the current Tailwind release ensures correct dark mode behavior and forward compatibility.
