Implement a connector for Azure Cosmos DB NoSQL to be used as a vector store backend in the Semantic Kernel framework. Ensure it supports creating, listing, and deleting vector collections, as well as upserting, retrieving, and deleting records with vector embeddings. Handle custom partition keys and key field mappings, and provide clear error messages for non-existent collections.

Requirements:

* Implement `AzureCosmosDBNoSQLCollection` class:
    * Accept parameters: `data_model_type`, `collection_name`, `database_name`, `url`, `key`, `cosmos_client`, `create_database` (default `False`), `data_model_definition`, `env_file_path`, `env_file_encoding`.
    * Default `partition_key.path` to `f'/{data_model_definition.key_field_name}'`.
    * Read configuration from environment variables: `AZURE_COSMOS_DB_NO_SQL_URL`, `AZURE_COSMOS_DB_NO_SQL_KEY`, `AZURE_COSMOS_DB_NO_SQL_DATABASE_NAME`.
    * Raise `MemoryConnectorInitializationError` for missing URL or database name, or invalid settings.
    * Create `CosmosClientWrapper` with `url` and `credential=key`; use Azure credential if no key is provided.
    * `_get_database_proxy()` should create the database if `create_database=True` and not found; otherwise, raise `MemoryConnectorResourceNotFound`.
    * `create_collection()` should call `create_container_if_not_exists` with specified parameters and raise `MemoryConnectorException` for unsupported vector field properties.
    * `delete_collection()` should call `delete_container(collection_name)` and raise `MemoryConnectorException` on failure.
    * `upsert(record)` should map non-"id" key fields to `COSMOS_ITEM_ID_PROPERTY_NAME` and return the key value.
    * `get(key, include_vectors=False)` should return a hydrated data model instance, mapping `COSMOS_ITEM_ID_PROPERTY_NAME` back to the model's key field name if needed.
    * Raise `MemoryConnectorException` for operations on non-existent collections.
    * Implement async context manager protocol to call `cosmos_client.close()` on exit.

* Implement `AzureCosmosDBNoSQLStore` class:
    * Accept parameters: `url`, `key`, `database_name`, `cosmos_client`, `create_database` (default `False`), `env_file_path`.
    * Expose properties: `database_name`, `cosmos_client`, `create_database`, `vector_record_collections`, `cosmos_db_nosql_settings`.
    * Raise `MemoryConnectorInitializationError` for missing URL or database name, or invalid settings.
    * `get_collection()` should create and register an `AzureCosmosDBNoSQLCollection`.
    * `list_collection_names()` should return a list of container names.
    * Implement async context manager protocol to call `cosmos_client.close()` on exit.

* Implement `AzureCosmosDBNoSQLCompositeKey` class:
    * Accept `key` and `partition_key` string parameters and expose them as attributes.

* Update `utils` module:
    * Define `COSMOS_ITEM_ID_PROPERTY_NAME` as `'id'`.
    * Implement `CosmosClientWrapper` with `url` and `credential`.
    * Implement `create_default_indexing_policy` and `create_default_vector_embedding_policy` to return dicts based on the vector store record definition.

* Update `to_weaviate_vector_distance` function in the Weaviate utils module:
    * Raise `ValueError` for `DistanceFunction.COSINE_SIMILARITY` and `DistanceFunction.EUCLIDEAN_DISTANCE`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.