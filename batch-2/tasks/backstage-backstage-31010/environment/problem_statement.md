## Description

The search backend plugin has no way to expose its search functionality through the backend actions registry. Other systems and agents that integrate via the actions API cannot currently perform search queries, limiting their ability to discover content indexed by Backstage.

## Expected Behavior

- A new action should be registered with the backend actions registry that allows callers to query the search engine.
- The action should accept a search term along with optional parameters for filtering by document type, applying field filters (including nested objects), and paginating results.
- All input fields should be forwarded to the underlying search engine as provided.
- Results should indicate whether additional pages are available.
- The total count of results (when available) should be included in the response.
- Sensitive internal authorization metadata must be stripped from result documents before they are returned.
- Results whose document location uses an unsafe URL scheme (anything other than standard web protocols) must be excluded from the response, and each exclusion should be logged.

## Why This Matters

Without this capability, AI agents and other action-based integrations cannot use the Backstage search index to look up catalog entities, documentation, or other indexed content. Exposing search through the actions API enables richer, search-driven workflows while keeping the response safe by sanitizing authorization data and dangerous URLs.
