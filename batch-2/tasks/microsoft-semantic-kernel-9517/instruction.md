Update the semantic kernel Python library to ensure that data-related types, mixins, constants, utilities, and exception classes are importable from top-level packages. Implement a new exception class for specific validation errors in vector store text search operations.

*   Ensure the following symbols are importable directly from the top-level `semantic_kernel.data` package:
    *   KernelSearchResults
    *   VectorizableTextSearchMixin
    *   VectorizedSearchMixin
    *   VectorSearchBase
    *   VectorSearchResult
    *   VectorTextSearchMixin
    *   TextSearchFilter
    *   VectorSearchFilter
    *   DEFAULT_DESCRIPTION
    *   DEFAULT_FUNCTION_NAME
    *   SearchOptions
    *   TextSearch
    *   TextSearchOptions
    *   TextSearchResult
    *   VectorSearchOptions
    *   create_options
    *   default_options_update_function
    *   VectorStoreRecordDataField
    *   VectorStoreRecordDefinition
    *   VectorStoreRecordKeyField
    *   VectorStoreRecordVectorField
    *   vectorstoremodel
    *   VectorStoreRecordCollection
    *   VectorStoreRecordUtils
    *   VectorStoreTextSearch

*   Ensure the following exception classes are importable directly from the top-level `semantic_kernel.exceptions` package:
    *   TextSearchException
    *   VectorStoreModelException
    *   MemoryConnectorException
    *   VectorStoreModelDeserializationException
    *   VectorStoreModelSerializationException
    *   VectorStoreTextSearchValidationError

*   Implement a new exception class `VectorStoreTextSearchValidationError` in `python/semantic_kernel/exceptions/` that is raised by `VectorStoreTextSearch` for validation failures.
    *   Ensure it is importable from `semantic_kernel.exceptions`.

*   Ensure `AzureTextEmbedding` is importable from `semantic_kernel.connectors.ai.open_ai`.

*   Ensure `KernelArguments` and `KernelParameterMetadata` are importable from `semantic_kernel.functions`.

*   Update `VectorStoreTextSearch` in `python/semantic_kernel/data/text_search/vector_store_text_search.py`:
    *   Raise `VectorStoreTextSearchValidationError` when instantiated with a vectorized_search collection but no embedder.
    *   Raise `VectorStoreTextSearchValidationError` when instantiated with no collections at all.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.