Implement two caching classes in the LangChain MongoDB partner library to support LLM response caching: an exact-match cache and a semantic cache. Ensure both caches integrate with the LangChain LLM cache system and support various prompt formats.

*   Create a `MongoDBCache` class in `libs/partners/mongodb/langchain_mongodb/cache.py`:
    *   Implement the `BaseCache` interface from `langchain_core.caches`.
    *   Accept `connection_string`, `collection_name` (default 'default'), and `database_name` (default 'default') as constructor parameters.
    *   Define a `_local_cache` attribute initialized as an empty dictionary.
    *   Implement `lookup(prompt: str, llm_string: str) -> Optional[RETURN_VAL_TYPE]`:
        *   Check `_local_cache` before querying MongoDB.
        *   Use `collection.find_one()` to retrieve the serialized response from the 'return_val' field.
        *   Return `None` on a cache miss.
    *   Implement `update(prompt: str, llm_string: str, return_val: RETURN_VAL_TYPE) -> None`:
        *   Use `collection.update_one()` with a `$set` operation and `upsert=True`.
    *   Implement `clear(**kwargs: Any) -> None`:
        *   Use `collection.delete_many()` with optional filter criteria.

*   Create a `MongoDBAtlasSemanticCache` class in `libs/partners/mongodb/langchain_mongodb/cache.py`:
    *   Inherit from `BaseCache` and `MongoDBAtlasVectorSearch`.
    *   Accept `connection_string`, `embedding`, `collection_name` (default 'default'), `database_name` (default 'default'), and `wait_until_ready` (default False) as constructor parameters.
    *   Define a class attribute `LLM` with the value "llm_string".
    *   Define instance attributes `_local_cache` (empty dict), `_wait_until_ready` (from constructor), and `_collection` (MongoDB Collection object).
    *   Implement `lookup(prompt: str, llm_string: str) -> Optional[RETURN_VAL_TYPE]`:
        *   Use an `aggregate()` call with a `$vectorSearch` stage and a filter on `llm_string`.
        *   Return results as a list of `Generation` objects.
    *   Implement `update(prompt: str, llm_string: str, return_val: RETURN_VAL_TYPE, wait_until_ready: Optional[bool] = None) -> None`:
        *   Store documents with fields: 'text', 'llm_string', 'embedding', and 'return_val'.
    *   Implement `clear(**kwargs: Any) -> None`:
        *   Use `collection.delete_many()` and clear `_local_cache`.
    *   Call `MongoDBAtlasVectorSearch.__init__()` in the constructor with the collection and embedding.

*   Ensure both caches integrate with the LangChain LLM cache system:
    *   Use `set_llm_cache()` to activate the cache for subsequent `.generate()` calls.
    *   Support plain string prompts, list-of-message prompts, and multiple generations per prompt.
    *   Serialize generations to a JSON-compatible string and deserialize them on retrieval.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.