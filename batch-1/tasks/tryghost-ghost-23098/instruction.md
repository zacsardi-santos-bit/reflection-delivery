Implement a service to fetch and enrich top content analytics data for the Ghost admin dashboard. Create a utility module for interacting with the Tinybird analytics API. Ensure the service handles errors gracefully and provides meaningful labels for unmatched content.

Requirements:

*   Implement `TopContentStatsService` class in `ghost/core/core/server/services/stats/TopContentStatsService.js`.
    *   Constructor: Accept `{ knex, urlService, tinybirdClient }` as parameters.
    *   `extractPostUuids(data: Array) -> Array<string>`: Extract non-empty, non-whitespace `post_uuid` values.
    *   `async lookupPostTitles(uuids: Array<string>) -> Object`: Return `{}` for empty `uuids`; query `posts` table for titles.
    *   `getResourceTitle(pathname: string) -> { title: string, resourceType: string } | null`: Use `urlService` to get resource title; return null if unavailable.
    *   `async enrichTopContentData(data: Array) -> Array`: Enrich data with titles; use pathname-derived labels for unresolved pages; label root as "Home".
    *   `async fetchRawTopContentData(options: Object) -> Array | null`: Convert options to camelCase; fetch data from Tinybird; return null on failure.
    *   `async getTopContent(options: Object) -> { data: Array }`: Fetch and enrich data; return empty array on error or if `tinybirdClient` is absent.

*   Implement `tinybird` module in `ghost/core/core/server/services/stats/utils/tinybird.js`.
    *   Export `create({ config, request }) -> TinybirdClient` factory function.
    *   `TinybirdClient` methods:
        *   `buildRequest(pipeName: string, options: Object) -> { url: string, options: Object }`: Construct API request URL and options; handle local mode and version suffix.
        *   `parseResponse(response) -> Array | null`: Parse various response formats into a data array; return null for unparseable JSON.
        *   `async fetch(pipeName: string, options: Object) -> Array | null`: Use `buildRequest` and `parseResponse`; return null on request errors or unparseable responses.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.