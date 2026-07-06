## Description

Setting up the Starlight sidebar is more work than it should be. Every page link in the sidebar requires an explicit label — even though that page already has a title in its frontmatter. For multilingual sites, this becomes even more tedious: every page link needs a translations map to provide locale-specific labels, duplicating information that's already defined in the translated content files.

It would be much better if you could simply reference a documentation page by its slug in the sidebar, and have Starlight automatically use the page's title (and locale-appropriate translated title) as the navigation label.

## Expected Behavior

- Sidebar items should support referencing a documentation page by its content slug, with the page's frontmatter title used automatically as the navigation label
- A plain string in the sidebar config should work as a shorthand for referencing a page by slug
- For multilingual sites, the sidebar should automatically use the translated page title for the current locale, falling back to the default locale's title when a translation doesn't exist
- Developers should still be able to optionally provide a custom label or per-locale label overrides directly in the sidebar item config
- If a slug is specified with a leading or trailing slash, a clear error message should explain the problem and suggest the corrected slug
- If a slug references a page that doesn't exist in the docs collection, a descriptive error message should guide the developer toward fixing the config

## Why This Matters

This reduces friction when building and maintaining documentation sidebars, especially for multilingual sites. It eliminates the need to manually duplicate page titles across the site config and content files, and keeps the sidebar in sync with page content automatically.
