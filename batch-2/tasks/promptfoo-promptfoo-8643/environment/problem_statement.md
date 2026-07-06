## Description

The Mistral provider currently builds cache keys from raw request data, meaning that sensitive information — including prompt text, API keys, and API endpoint addresses — can appear directly in cache identifiers and debug log output. This is a privacy and security problem, particularly in shared or multi-tenant deployments where logs and cache stores may be visible to multiple parties.

Additionally, when two concurrent requests with identical parameters are made, the provider makes redundant network calls instead of deduplicating them. And when embedding responses are served from cache, the result does not indicate this was a cached response.

## Expected Behavior

- Cache keys for both chat completion and embedding requests should be composed of cryptographic hashes of their components, so that sensitive data (prompt text, API credentials, endpoint URLs) never appears in plain text in any cache key or log.
- Two providers with the same model but different API credentials must produce different cache keys, even for identical inputs — preventing cross-tenant cache collisions.
- Cache key hashes must be stable and deterministic across application restarts or module reloads.
- When identical concurrent requests are in flight, only one actual network call should be made; all callers should receive the same result.
- This deduplication must be properly scoped: requests made in different operational scopes should not share deduplication state.
- Debug logs must not contain raw prompt text, API key values, or generated model outputs.
- Responses served from cache (for both chat and embeddings) must indicate that they came from cache.

## Why This Matters

Without this fix, debug logs and cache storage can inadvertently expose sensitive user data and credentials. In multi-tenant or production environments, this represents a real data leakage risk. Deduplication also reduces unnecessary API calls and latency.
