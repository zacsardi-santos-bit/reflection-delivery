Reorganize and extend the request data utilities in the Sentry JavaScript SDK core package. Move existing utilities for cookie parsing and IP address extraction to more appropriate locations. Implement a new module to extract normalized request data from both traditional Node.js HTTP requests and modern edge-runtime requests.

*   Move and export the `parseCookie` function from `packages/core/src/utils-hoist/cookie.ts` to `packages/core/src/utils/cookie.ts`.
    *   Ensure it retains its original cookie-parsing behavior.
*   Move and export the `getClientIPAddress` function from `packages/core/src/utils-hoist/vendor/getIpAddress.ts` to `packages/core/src/vendor/getIpAddress.ts`.
    *   Ensure it retains its original IP address extraction behavior.

*   Implement and export the following functions from `packages/core/src/utils/request.ts`:

    *   `winterCGHeadersToDict(headers: { forEach?: (callbackfn: (value: unknown, key: string) => void) => void }): Record<string, string>`
        *   Convert a WinterCG-compatible headers object to a plain string dictionary.
        *   Include only entries where the value is a string.
        *   Return an empty object if the headers object lacks a `forEach` method.

    *   `headersToDict(headers: Record<string, string | string[]>): Record<string, string>`
        *   Convert a plain object with string or string-array header values to a string-only dictionary.
        *   Exclude entries where the value is an array.

    *   `winterCGRequestToRequestData(request: { method: string; url: string; headers: any; clone: () => any }): { headers: Record<string, string>; method: string; query_string: string | undefined; url: string }`
        *   Extract normalized request data from a WinterCG-compatible request object.
        *   Return an object with filtered headers, method, url, and query_string.

    *   `httpRequestToRequestData(request: { url?: string; method?: string; headers?: Record<string, string | string[]>; protocol?: string; socket?: { encrypted?: boolean }; cookies?: Record<string, string>; body?: unknown }): { headers: Record<string, string>; method?: string; url?: string; query_string?: string; cookies?: Record<string, string>; data?: unknown }`
        *   Extract normalized request data from a Node.js HTTP-style request object.
        *   Construct the full URL from host header and protocol/socket when only a relative path is available.
        *   Filter headers to include only string values.
        *   Map a 'body' property to 'data' in the result.
        *   Return `{ headers: {} }` for an empty input.

    *   `extractQueryParamsFromUrl(url: string | undefined): string | undefined`
        *   Extract the query string portion from a URL string.
        *   Return undefined if the URL is undefined or contains no '?'.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.