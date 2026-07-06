## Description

The Supabase documentation site exposes a GraphQL API, but it currently has no way to search documentation content. Developers or internal tools that rely on this API have no mechanism for finding relevant docs pages programmatically.

## Expected Behavior

- The GraphQL API should expose a search query that accepts a text string and returns a list of matching documentation pages.
- Each result should include at minimum the page title and its URL.
- Callers should be able to request the full text content of matching pages as an optional field.
- A limit argument should allow callers to control how many results are returned.
- If the search query argument is omitted, the API should return a validation error indicating the argument is required.
- If the underlying search infrastructure encounters an error (e.g., during AI embedding generation), the API should return a generic internal error rather than leaking implementation details.

## Why This Matters

Without this feature, any client consuming the GraphQL API has no way to implement documentation search. Adding semantic search through the GraphQL layer makes the docs searchable in a consistent, type-safe way that integrates with the existing API surface. The use of AI-generated embeddings enables relevance-based results rather than simple keyword matching.
