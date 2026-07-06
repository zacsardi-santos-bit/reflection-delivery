## Description

The HTTP fetch caching layer has several correctness and security issues with how it generates cache keys and handles concurrent requests.

**Cache key correctness problems:**

- Requests that differ only in their headers — including authentication tokens — are treated as the same cached request, so responses may be served to the wrong caller.
- When a request object with embedded headers is passed to the fetch wrapper and no additional headers are specified in the options, those embedded headers are silently dropped, causing authentication credentials to be lost.
- HTTP method names using different casing (e.g. lowercase versus uppercase) produce different cache keys and result in duplicate network calls.
- Options objects with the same fields in different property order produce different cache keys instead of the same one.
- Requests for different response formats (such as structured data or plain text) to the same URL share a cache entry, causing the wrong format to be returned.

**Cache key security problems:**

- Sensitive values such as API tokens, authorization headers, and URL query parameters containing secrets are stored verbatim in cache keys, potentially exposing credentials.

**Incorrect caching of non-cacheable requests:**

- Requests with multipart form data bodies are incorrectly cached, causing responses for different payloads to be mixed up.
- Requests with non-serializable transport options bypass the cache inconsistently.
- When a request object's body is a stream, an explicit absent-body override in the options was incorrectly treated as "no body," masking the stream and producing wrong cache keys.

**In-flight deduplication problems:**

- When two concurrent callers make requests to the same URL — one with an abort signal and one without — they are incorrectly deduplicated. Aborting the signaled caller's request also cancels the unsignaled caller's request.

## Expected Behavior

- Requests with different headers should produce different cache entries.
- Actual secret values (tokens, keys) should be hashed before being incorporated into cache keys, not stored in plain text.
- Multipart form data bodies, non-serializable transport options, and stream-based request bodies should bypass the cache gracefully.
- Abort signals should isolate in-flight requests per-signal, so cancelling one caller does not affect others.
- HTTP method casing and option property order should be normalized so equivalent requests share a single cache entry.
- The response format should be part of the cache key to prevent format mismatches.
- Headers embedded in a request object should be preserved when no init headers are provided, and replaced when init headers are explicitly passed.
- Cloud API authentication should be added only when the request targets the exact configured cloud host — not any lookalike domain.

## Why This Matters

These bugs can cause incorrect responses to be served from cache, authentication credentials to be silently dropped, secrets to be exposed in cache storage, and unrelated concurrent requests to be cancelled when one caller aborts.
