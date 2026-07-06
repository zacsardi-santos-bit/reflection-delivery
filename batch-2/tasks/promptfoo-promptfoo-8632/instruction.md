I'm running into several issues with the HTTP fetch caching layer that are causing incorrect behavior and potential security problems.

*   fetchWithProxy must preserve a Request object's own headers (lowercased) in the outgoing fetch call when no init headers are provided (options.headers is undefined). These headers must be merged with the version header.

*   fetchWithProxy must completely replace a Request object's headers with the provided init headers when options.headers is defined (including when it is an empty object {}). Only init headers and the version header are forwarded — the Request's embedded headers are dropped.

*   isPromptfooCloudApiHost must return true only when the parsed URL's origin exactly equals the cloud API host. It must return false for any lookalike hostname that merely contains or extends the cloud hostname (e.g. a domain like api.promptfoo.dev.evil.example must not match). Invalid URLs must return false.

*   fetchWithProxy must add the cloud Authorization header (Bearer token from cloudConfig.getApiKey()) only when isPromptfooCloudApiHost returns true for the target URL. Lookalike or unrelated URLs must not receive this header.

*   getFetchWithProxyHeaders must be exported from src/util/fetch/index.ts so that other modules (including the cache layer) can compute the expected merged headers for a request without invoking fetch.

*   fetchWithCache must track in-flight requests separately for calls that carry an AbortSignal versus calls that do not. Aborting a signaled in-flight request must not cancel or fail a concurrent unsignaled request to the same URL, and a new unsignaled request must issue its own network call rather than joining the signaled in-flight entry.

*   fetchWithCache must not cache requests whose body is a FormData instance. cache.set must not be called for such requests, and each call must result in a separate network fetch.

*   fetchWithCache must treat an explicit null value in init.body as absent (not as 'no body'). When init.body is null and the first argument is a Request with a body, the Request's body is used for cache-key computation. Because a Request's body is a ReadableStream (non-serializable), the request must not be cached.

*   fetchWithCache must not cache requests that include non-primitive, non-standard fetch options (such as a dispatcher object with object-typed values). Such requests must bypass the cache entirely and each call must result in a separate network fetch.

*   fetchWithCache must include request headers in the cache key so that requests with different headers produce different cache entries. However, the actual header values must NOT appear verbatim in the cache key string — they must be hashed or otherwise obfuscated.

*   fetchWithCache must include the cloud API key (from cloudConfig.getApiKey()) in the cache key for requests to the cloud API host, so that calls with different API keys produce different cache entries. The API key value itself must NOT appear verbatim in the cache key string.

*   fetchWithCache must include headers embedded in a Request object in the cache key. When init.headers is defined (even as {}), init headers replace the Request's own headers in the key. Header values must NOT appear verbatim in the cache key string.

*   fetchWithCache must normalize the HTTP method to uppercase before computing the cache key, so that 'get' and 'GET' produce identical cache keys and result in a cache hit.

*   fetchWithCache must canonicalize the order of primitive fetch options (excluding headers, signal, and body) before computing the cache key, so that the same options specified in different property orders produce the same cache key.

*   fetchWithCache must include the response format argument ('json' or 'text') in the cache key, so that a request for JSON and the same request for text produce different cache entries and each triggers a separate network fetch.


*   Interface details: Type: Function
Name: getFetchWithProxyHeaders
Location: src/util/fetch/index.ts
Signature: getFetchWithProxyHeaders(url: RequestInfo, options: FetchOptions): Record<string, string>
Description: Computes the final merged headers for a proxied fetch call. When `url` is a `Request` object and `options.headers` is `undefined`, the Request's own headers are extracted (lowercased via the Headers API) and merged with `{ 'x-promptfoo-version': VERSION }`. When `options.headers` is defined (even as an empty object `{}`), only `options.headers` merged with `{ 'x-promptfoo-version': VERSION }` are returned — the Request's own headers are NOT included. Must be exported from this module.

Type: Function
Name: isPromptfooCloudApiHost
Location: src/util/fetch/monkeyPatchFetch.ts
Signature: isPromptfooCloudApiHost(url: string | URL | Request): boolean
Description: Returns true only when the URL's origin exactly equals the configured cloud API host constant (CLOUD_API_HOST). For a `Request` input, uses `url.url`. For invalid/unparseable URLs, returns false. Must be exported from this module.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.