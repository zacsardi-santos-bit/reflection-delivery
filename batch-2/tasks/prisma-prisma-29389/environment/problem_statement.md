## Description

Prisma Studio currently loads its front-end dependencies from external CDN services using an import map in the HTML shell. This creates a hard dependency on internet connectivity at runtime, which breaks Studio for users in air-gapped environments or with strict network policies. The HTML page references multiple third-party CDN URLs, meaning Studio cannot function without a live internet connection.

We should switch to a fully self-contained, bundled distribution: the Studio JavaScript and CSS assets should be pre-bundled and served directly from the local Prisma server instead of being fetched from the internet. The HTML shell should reference these local assets rather than external URLs, with no import maps or CDN references remaining.

## Expected Behavior

- The HTML page served by Studio must not contain any import maps, CDN references, or dynamically resolved module URLs.
- A local JavaScript bundle and CSS bundle are served as static assets directly from the Studio server.
- The HTML shell links to these local assets and injects a configuration object for the active database adapter.
- All responses (HTML, assets, API calls) include permissive cross-origin headers so browser tooling can reach the Studio backend.
- The Studio server also handles browser preflight requests correctly and returns informative error responses when the backend fails.
- Fetching a previously available adapter-specific script endpoint now returns a not-found response, as adapters are now part of the unified bundle.

## Why This Matters

Users in restricted or offline environments are currently unable to use Studio at all. Bundling the assets locally makes Studio a fully self-contained tool that works regardless of network access, and removes the operational risk of third-party CDN outages affecting developer workflows.
