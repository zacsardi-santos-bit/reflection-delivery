I've been looking at how the Mistral provider handles caching and logging, and I'm concerned about sensitive data leakage.

*   MistralChatCompletionProvider.callApi must call fetchWithCache with exactly 5 arguments: (url, requestInit, timeoutMs, 'json', true) — passing the literal string 'json' as the 4th argument and the boolean true as the 5th argument.

*   MistralChatCompletionProvider.callApi must include the HTTP header 'x-promptfoo-silent' set to the string 'true' in every request sent to the Mistral API.

*   MistralChatCompletionProvider.callApi must construct cache keys in the format 'mistral:chat:<model>:<hash1>:<hash2>:<hash3>' where each hash is a 64-character lowercase hexadecimal string (SHA-256 or HMAC-SHA256). The three hashes must correspond to distinct components of the request (e.g., request parameters/body, resolved API key, resolved API base URL) — none of these values should appear in raw form in the cache key.

*   MistralChatCompletionProvider.callApi must isolate cache entries by resolved API key: two providers configured with the same model but different API keys must produce different cache keys even for identical prompts.

*   MistralChatCompletionProvider.callApi must produce stable, deterministic cache keys: the same API key, prompt, and API URL must hash to the same cache key value across module reloads (the hash algorithm must not use random per-session seeds).

*   MistralChatCompletionProvider.callApi must deduplicate concurrent in-flight requests with the same cache key: if two calls with identical parameters are made concurrently, only one fetchWithCache call should be made and both callers receive the same result.

*   In-flight request deduplication must be scoped by the active cache namespace: calls made under different withCacheNamespace scopes must each result in a separate fetchWithCache call even with identical parameters. The getScopedCacheKey function exported from src/cache.ts is the mechanism for obtaining the namespace-scoped key.

*   MistralChatCompletionProvider.callApi must not include raw prompt text, API key values, or generated model output in any debug log output.

*   MistralEmbeddingProvider.callEmbeddingApi must call fetchWithCache with exactly 5 arguments: (url, requestInit, timeoutMs, 'json', true) and must include the HTTP header 'x-promptfoo-silent' set to 'true' in every request.

*   MistralEmbeddingProvider.callEmbeddingApi must construct cache keys in the format 'mistral:embedding:<model>:<hash1>:<hash2>:<hash3>' where each hash is a 64-character lowercase hexadecimal string. Raw input text and API key must not appear in the cache key.

*   MistralEmbeddingProvider.callEmbeddingApi must return a result with 'cached: true' when the response is served from the provider-level cache store (getCache) without calling fetchWithCache. The cache store lookup must use the hashed cache key.

*   MistralEmbeddingProvider.callEmbeddingApi must not include raw input text, API key values, or embedding vector values in any debug log output.

*   MistralEmbeddingProvider.callApi must return a ProviderResponse that includes 'cached: true' and output set to the JSON-stringified embedding array when the embedding is served from cache.

*   getScopedCacheKey must be exported (made public) from src/cache.ts so that the Mistral provider can import and use it to scope in-flight request deduplication by the current cache namespace context.


*   Interface details: Type: Class
Name: MistralChatCompletionProvider
Location: src/providers/mistral.ts
Description: Provider for Mistral chat completion API. Must be updated to use hashed cache keys, include a silent request header, deduplicate in-flight requests in a namespace-aware manner, and suppress logging of sensitive content.
Signature:
  callApi(prompt: string, context?: CallApiContextParams, callApiOptions?: CallApiOptionsParams) -> Promise<ProviderResponse>

Type: Class
Name: MistralEmbeddingProvider
Location: src/providers/mistral.ts
Description: Provider for Mistral embedding API. Must be updated to use hashed cache keys, include a silent request header, and return a result with cached: true when data is served from the provider-level cache store without making a network call. The callApi method must also propagate cached: true from the underlying embedding response.
Signature:
  callEmbeddingApi(input: string) -> Promise<ProviderEmbeddingResponse>
  callApi(input: string) -> Promise<ProviderResponse>

Type: Function
Name: getScopedCacheKey
Location: src/cache.ts
Signature: getScopedCacheKey(cacheKey: string, namespace?: string) -> string
Description: Must be exported (made public) from src/cache.ts. Returns the cache key prefixed with the current namespace scope if one is active, otherwise returns the key unchanged. The Mistral provider uses this to scope in-flight request deduplication by the current withCacheNamespace context. Note: withCacheNamespace already exists in src/cache.ts and does not need to be created.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.