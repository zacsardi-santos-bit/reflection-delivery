## Description

Header navigation tab links do not respect the router's configured base path when the application is deployed under a URL sub-path.

## Current Behavior

When using the header navigation component with a router that has a base path configured (e.g., the app is served at a sub-path rather than the root), flat navigation tabs generate link URLs that are missing the base path prefix. Users clicking on these tabs are taken to incorrect, broken URLs.

## Expected Behavior

- Flat navigation tab links should include the router's base path in their href, so that the link resolves correctly when the app is deployed at a sub-path.
- Active tab detection should work correctly — the tab whose path matches the current location (including base path) should be visually marked as active, while other tabs should not.
- Grouped (dropdown) navigation tab items should also have their hrefs include the router's base path (this already works today; the fix should bring flat tabs into parity).

## Why This Matters

Applications deployed under a sub-path need all navigation links to be base-path-aware. Without this fix, header navigation is broken for any Backstage deployment that configures a router base path, sending users to invalid routes instead of the intended destinations.
