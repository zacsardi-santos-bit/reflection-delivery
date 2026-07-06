## Description

When a CSS file is configured to export its content as a raw text string and is imported at the CSS level by another CSS file configured to inject its styles into the page, the imported file's styles are not being applied to the page. Instead of creating a style tag for the imported content, webpack treats the imported file purely as text — meaning its CSS rules are silently ignored and do not take effect in the browser.

## Expected Behavior

- When a CSS file is imported at the CSS level by a CSS file that is configured to inject styles into the DOM, the imported file's content should also be injected as an active stylesheet, regardless of the imported file's own export type configuration.
- The parent module's export type should take precedence when resolving how a CSS-level imported dependency is handled.
- Directly importing the text-type CSS file in JavaScript should still return its content as a string.
- CSS Modules class name exports from the style-type parent should continue to work correctly.

## Why This Matters

This is a subtle but impactful bug: a developer who imports a utility CSS file (configured as text for some use cases) from within a style-injecting CSS module will find that the utility styles are never applied to the page. There is no error or warning — the styles are just silently absent. The fix ensures that the CSS injection chain works correctly when mixing export types through CSS-level imports.
