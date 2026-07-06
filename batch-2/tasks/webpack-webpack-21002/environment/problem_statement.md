## Description

When using HTML files as webpack entry points with both the HTML and CSS experimental features enabled, CSS that is imported through JavaScript is not being reflected in the generated HTML output. The CSS files are emitted to disk correctly, but the HTML file that webpack produces does not include the necessary stylesheet link tags — so browsers never actually load the styles.

This affects several related scenarios:

- A JavaScript entry imported by an HTML script tag imports one or more CSS files. The CSS is compiled and emitted, but no stylesheet link appears in the HTML.
- The HTML entry's script tag carries security attributes (like a content security policy nonce, cross-origin mode, and referrer policy). Any auto-generated stylesheet links should inherit those attributes so the browser will fetch the stylesheet under the same security policy. Script-only attributes (like deferred loading markers and integrity hashes) should not carry over to stylesheet links.
- When CSS is split into multiple separate chunks by the optimizer, the HTML output should include one stylesheet link per CSS chunk, in the same order as the original CSS imports in JavaScript.
- Mixed HTML entries that combine a directly authored stylesheet link with a script that imports additional CSS should produce both links in the correct order (the authored link before the JS-bundle CSS link), both appearing before the script tag.
- HTML entries using a separate runtime chunk and a vendor split chunk must still produce script tags in the correct dependency order (runtime first, then vendor, then the entry chunk), while all stylesheet links continue to precede all script tags.

## Expected Behavior

- A stylesheet link tag is emitted in the output HTML for every CSS chunk associated with a JavaScript entry in that HTML file.
- Security and fetch attributes from the originating script tag propagate to auto-generated stylesheet links; script-only attributes do not.
- Multiple CSS chunks appear as multiple stylesheet links in source import order.
- All stylesheet links appear before any script tags in the output HTML.
- Runtime and vendor chunk ordering is preserved correctly even when CSS links are also present.

## Why This Matters

Without this fix, applications relying on webpack's HTML entry pipeline with JavaScript-imported CSS will have their styles silently dropped from the browser's loading process. This is especially impactful in environments with strict content security policies, where the missing nonce on a stylesheet link would cause the browser to block the stylesheet even if it were present.
