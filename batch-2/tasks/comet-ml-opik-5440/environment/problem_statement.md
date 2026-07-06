## Description

Throughout the application, we display various text content — error messages, log output, user-generated strings, and configuration data — that often contains URLs. Currently, those URLs are rendered as plain text, requiring users to manually copy and paste them into a browser. We need a reusable component that automatically detects web links within text and renders them as clickable anchors.

## Expected Behavior

- Any web URL (using the standard web protocols) embedded in text content should automatically become a clickable link that opens in a new browser tab.
- Links should open in a new tab and should not pass click events up to parent elements.
- Links should be visually styled to be recognizable as links (blue, underlined).
- URLs followed by punctuation (such as periods, commas, or enclosing brackets) should be correctly identified with the punctuation excluded from the link target.
- Multiple URLs in the same text block should each become separate links.
- Non-web protocols and bare domain names without a protocol should remain as plain text — never linkified.
- The component must be safe to use with untrusted input: dangerous URI schemes must never produce clickable links, and any script-like content in the input must not be executed.
- When there are no URLs in the text, the output should be the plain text with no extra markup added.
- The component must handle edge cases gracefully: empty strings, whitespace-only strings, null or undefined content, and numeric values.

## Why This Matters

Users frequently see URLs embedded in outputs and have to manually copy them. A reusable text component that auto-detects and linkifies URLs improves usability across the application and reduces friction — especially in contexts like error messages or API response previews where links to documentation or endpoints commonly appear.
