Implement a context-aware facet search functionality that distinguishes between search and listing page contexts in API requests. Ensure all facet search requests, including field suggestions, consistently include a query field. Update the facet controller configuration to specify context types, and ensure sub-controllers automatically apply the correct context type.

*   Export the `FacetSearchType` type from `packages/headless/src/api/commerce/facet-search/facet-search-request.ts` as a union type with values 'SEARCH' and 'LISTING'.
*   Modify the `facetSearch` method in the commerce API client to accept a second parameter of type `FacetSearchType`. Append a 'type' query parameter to the URL using this value.
*   Update `executeCommerceFacetSearch` and `executeCommerceFieldSuggest` async thunk action creators to accept an object with `facetId` (string) and `facetSearchType` (FacetSearchType). Reflect this structure in action metadata.
*   Define `RegularFacetSearchProps` as a named exported interface in `packages/headless/src/controllers/commerce/core/facets/regular/headless-commerce-regular-facet-search.ts`. Ensure it includes an `options` field with `facetId` (string) and `type` (FacetSearchType), excluding `executeFacetSearchActionCreator` and `executeFieldSuggestActionCreator`.
*   Ensure options/props for commerce facet controllers include a required `facetSearch` property with a `type` field of `FacetSearchType`.
*   Add a `facetSearchType` field of type `FacetSearchType` to the `SearchAndListingSubControllerProps` interface in `packages/headless/src/controllers/commerce/core/sub-controller/headless-sub-controller.ts`.
*   Ensure `buildFacetSearchRequest` and `buildCategoryFacetSearchRequest` include a `query` field with an empty string value in field suggestion requests.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.