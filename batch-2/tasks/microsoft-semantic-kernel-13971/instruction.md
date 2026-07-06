I'm running into an issue with the Azure AI Search vector store.

*   The `_resolve_credential` function must be exported from the `semantic_kernel.connectors.azure_ai_search` module.

*   The `_resolve_credential` function must accept an `AzureAISearchSettings` object as its first argument, an optional `AzureKeyCredential` as `azure_credential`, and an optional `AsyncTokenCredential` as `token_credential`. It must return the provided `azure_credential` if given, or the provided `token_credential` if given, or a key credential derived from the settings API key.

*   When `_resolve_credential` is called with no credential arguments and the settings has no API key available, it must raise `ServiceInitializationError`.

*   The `AzureAISearchStore` class must expose a `search_endpoint` attribute (type `str | None`, default `None`) and a `search_credential` attribute (any credential type, default `None`).

*   When `AzureAISearchStore` is instantiated by providing a pre-built `search_index_client` directly, both `search_endpoint` and `search_credential` must be `None` on the resulting store instance.

*   Calling `get_collection()` on an `AzureAISearchStore` created with a pre-built `search_index_client` (where `search_endpoint` and `search_credential` are `None`) must succeed and return a collection with a non-None `search_client` and matching `search_index_client`.

*   The `AzureAISearchCollection` class must expose `search_endpoint` (type `str | None`, default `None`) and `search_credential` (any credential type, default `None`) attributes. When a collection is obtained via `AzureAISearchStore.get_collection()`, its `search_endpoint` must equal the store's `search_endpoint` and its `search_credential` must equal the store's `search_credential`.

*   The `_get_search_index_client` function must accept `AsyncTokenCredential` (not the synchronous `TokenCredential`) as its `token_credential` parameter type, and must raise `ServiceInitializationError` when no valid credential can be found.


*   Interface details: Type: Function
Name: _resolve_credential
Location: python/semantic_kernel/connectors/azure_ai_search.py
Signature: _resolve_credential(azure_ai_search_settings: AzureAISearchSettings, azure_credential: AzureKeyCredential | None = None, token_credential: AsyncTokenCredential | None = None) -> AzureKeyCredential | AsyncTokenCredential
Description: Resolves the credential to use for Azure AI Search. Returns azure_credential if provided, otherwise returns token_credential if provided, otherwise creates an AzureKeyCredential from the settings api_key. Raises ServiceInitializationError if no credential can be resolved (i.e., no azure_credential, no token_credential, and no api_key in settings). Must be exported from the module so it can be imported directly from semantic_kernel.connectors.azure_ai_search.

Type: Class
Name: AzureAISearchStore
Location: python/semantic_kernel/connectors/azure_ai_search.py
Description: Azure AI Search vector store. Must expose two new class-level attributes: search_endpoint (str | None, default None) and search_credential (Any, default None). When instantiated with only a pre-built search_index_client and no other connection parameters, search_endpoint and search_credential must be None. The get_collection() method must accept search_credential and search_endpoint as keyword arguments (forwarded to the collection) and must still succeed when these are None on the store (falling back to environment variables for credential resolution).

Type: Class
Name: AzureAISearchCollection
Location: python/semantic_kernel/connectors/azure_ai_search.py
Description: Azure AI Search collection. Must expose two new class-level attributes: search_endpoint (str | None, default None) and search_credential (Any, default None). These attributes must be populated to match the store's corresponding attributes when a collection is created via AzureAISearchStore.get_collection(). The __init__ method must accept a search_credential parameter (AzureKeyCredential | AsyncTokenCredential | None = None).

Type: Function
Name: _get_search_index_client
Location: python/semantic_kernel/connectors/azure_ai_search.py
Signature: _get_search_index_client(azure_ai_search_settings: AzureAISearchSettings, azure_credential: AzureKeyCredential | None = None, token_credential: AsyncTokenCredential | None = None) -> SearchIndexClient
Description: Existing function updated to use AsyncTokenCredential (not the synchronous TokenCredential) as the type for token_credential. Raises ServiceInitializationError when no credentials are available.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.