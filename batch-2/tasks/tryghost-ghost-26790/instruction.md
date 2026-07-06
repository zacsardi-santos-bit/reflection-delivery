I'm working on the Transistor podcast embed card renderer in Ghost.

*   The transistor renderer's web output must render an actual iframe embed instead of a static placeholder. The output must contain an <iframe> element with a `data-src` attribute (not `src`) set to `https://partner.transistor.fm/ghost/embed/{uuid}` (where `{uuid}` remains as a literal placeholder to be substituted at request time).

*   The iframe element must include a `data-kg-transistor-embed` attribute.

*   When the renderer receives a `siteUuid` value in its options, the embed URL must append it as a `ctx` query parameter (e.g. `https://partner.transistor.fm/ghost/embed/{uuid}?ctx={siteUuid}`).

*   When no `siteUuid` is provided in the options (empty options object), the embed URL must not include any `ctx` query parameter.

*   The rendered HTML must include a `<noscript>` element containing a fallback `<iframe>` with a regular `src` attribute (not `data-src`) pointing to the same embed URL (including any `ctx` param if applicable).

*   The rendered HTML must include a `<script>` block. The script block must contain the background-detection logic, and the string `setSrcBackgroundFromParent` must appear in the rendered output as the name of the injected function.


*   Interface details: Type: Module
Name: transistor-renderer
Location: ghost/core/core/server/services/koenig/node-renderers/transistor-renderer.js
Description: The transistor node renderer for Ghost's Koenig editor. The module's default export is a function that renders a Transistor podcast embed card. The web (frontend) rendering path must now produce an iframe-based embed instead of a static placeholder.

Key output requirements for the web rendering path:
- Must include an `<iframe>` with attribute `data-src` set to `https://partner.transistor.fm/ghost/embed/{uuid}` (with the literal string `{uuid}` as a placeholder)
- Must include attribute `data-kg-transistor-embed` on the iframe
- When `options.siteUuid` is provided, the `data-src` URL must append `?ctx={siteUuid}` as a query parameter
- When `options.siteUuid` is absent or empty, the URL must have no `ctx` query parameter at all
- Must include a `<noscript>` element containing a fallback `<iframe>` with a regular `src` (not `data-src`) using the same URL
- Must include a `<script>` block whose content contains the string `setSrcBackgroundFromParent` (this is the name of the inline background-detection function serialized into the rendered HTML)


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.