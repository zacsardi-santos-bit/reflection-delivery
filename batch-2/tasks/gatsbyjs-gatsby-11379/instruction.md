Implement the `onRenderBody` function in `packages/gatsby-plugin-google-tagmanager/src/gatsby-ssr.js` to support setting up a default data layer before the GTM script loads. Ensure the function handles both static objects and dynamic functions for data layer initialization and maintains a single-line output format.

Requirements:

*   Export `onRenderBody` from `packages/gatsby-plugin-google-tagmanager/src/gatsby-ssr.js`.
*   Use `setHeadComponents` to add a React script element with `dangerouslySetInnerHTML.__html` containing the GTM loader script as a single line.
*   Use `setPreBodyComponents` to add a React noscript element with `dangerouslySetInnerHTML.__html` containing the GTM iframe tag as a single line.
*   Handle `pluginOptions.defaultDataLayer`:
    *   If not present, ensure the head component HTML does not include 'window.dataLayer' or 'undefined'.
    *   If `{ type: 'object', value: plainObject }`, prepend `'window.dataLayer = window.dataLayer || [];window.dataLayer.push('` followed by `JSON.stringify(value)` and `');'` before the GTM script.
    *   If `{ type: 'function', value: functionString }`, prepend `'window.dataLayer = window.dataLayer || [];window.dataLayer.push(('` followed by the function string as a single line, then `')());'` before the GTM script.
    *   If `type` is neither 'object' nor 'function', call `reporter.panic()` with an error message.
    *   If `type` is 'object' but `value` is not a plain object, call `reporter.panic()` with an error message.
*   Ensure the complete head script HTML, including any defaultDataLayer prefix and the GTM loader, is rendered as a single line.
*   Ensure the pre-body HTML contains no newline characters.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.