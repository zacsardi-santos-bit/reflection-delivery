## Description

The commerce facet search feature does not distinguish between a search page context and a product listing page context when making facet search requests to the backend API. All requests look identical regardless of where they are triggered from, which means the backend cannot determine how to handle them appropriately based on page type.

Additionally, field suggestion requests are missing a query field that is part of the standard API contract for facet search requests, causing inconsistently structured requests compared to regular facet search requests.

## Expected Behavior

- Facet search requests should carry information about the page context (search vs. listing) so the backend API receives it as a parameter in the request URL.
- All facet search request objects — including those for field suggestions — should consistently include a query field.
- The facet controller configuration options should include a way to specify which context type (search or listing) they belong to, and this should propagate through to the underlying API calls.
- Sub-controllers for search and listing pages should automatically apply the correct context type without requiring consumers to provide it manually for each controller.

## Why This Matters

Without context-aware facet search requests, the backend API cannot correctly distinguish between search and listing facet queries, which may result in incorrect behavior or results in either context. Consistently including the query field ensures all requests conform to the expected API contract regardless of request type.
