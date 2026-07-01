## Description

The LangChain MongoDB partner library has no caching support for LLM responses. Every call to a language model — even for an identical prompt — results in a fresh API call, with no way to retrieve or reuse a previously computed response.

Two caching backends should be added to the library:

1. **Exact-match cache** — stores responses keyed by the exact prompt and model identifier. When the same prompt is submitted again, the cached response is returned immediately without calling the model.

2. **Semantic cache** — stores responses alongside their vector embeddings. When a new prompt arrives, it is compared semantically to previously cached prompts using MongoDB Atlas vector search. If a sufficiently similar prompt is found, its cached response is returned. This cache should inherit from the existing MongoDB Atlas vector store class.

## Expected Behavior

- Both caches should implement the standard LangChain cache interface and be registerable as the global LLM cache.
- Once set as the active cache, subsequent LLM and chat model calls should transparently hit the cache without any changes to calling code.
- Both caches should support plain text prompts, multi-turn chat message lists, and multiple generations per prompt.
- Both caches should provide a way to clear cached entries, optionally filtered by criteria.
- The semantic cache should accept an embedding model, a MongoDB connection string, a collection name, and a database name.
- The exact-match cache should accept a MongoDB connection string, a collection name, and a database name.

## Why This Matters

Teams using MongoDB Atlas as their primary data store want a single infrastructure dependency for both their data and their LLM caching layer. Without native caching support in the library, they are forced to use a separate cache integration or make redundant model API calls, increasing latency and cost.
