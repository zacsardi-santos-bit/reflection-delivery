Implement support for managing machine learning models and their versions in the Python client library for a metadata catalog system. Create a new catalog type for models, allowing operations such as registering models, tracking versions, retrieving metadata, and deleting models or versions. Reorganize existing catalog-related modules into a more focused client package.

*   Relocate existing modules:
    *   Ensure `FilesetCatalog` is importable from `gravitino.client.fileset_catalog`.
    *   Ensure `DTOConverters` is importable from `gravitino.client.dto_converters`.
    *   Ensure `BaseSchemaCatalog` is importable from `gravitino.client.base_schema_catalog`.

*   Implement `GenericModelCatalog` in `clients/client-python/gravitino/client/generic_model_catalog.py`:
    *   Constructor must accept `namespace`, `name`, `catalog_type`, `provider`, `comment`, `properties`, `audit`, `rest_client`.
    *   Implement `as_model_catalog()` to return an object exposing the model catalog API.
    *   Implement `list_models(namespace)` to return a list of `NameIdentifier` objects.
    *   Implement `get_model(ident)` to return an object with `name()`, `comment()`, `properties()`, `latest_version()`.
    *   Implement `register_model(ident, comment, properties)` to return a `Model` object.
    *   Implement `delete_model(ident)` to return `True` if deletion is confirmed, `False` if the model did not exist.
    *   Implement `list_model_versions(ident)` to return a list of version numbers.
    *   Implement `get_model_version(ident, version)` to return an object with `version()`, `uri()`, `aliases()`, `comment()`, `properties()`.
    *   Implement `get_model_version_by_alias(ident, alias)` to return a `ModelVersion` object.
    *   Implement `link_model_version(ident, uri, aliases, comment, properties)` to return `None` on success.
    *   Implement `delete_model_version(ident, version)` to return `True` if deleted, `False` if the version did not exist.
    *   Implement `delete_model_version_by_alias(ident, alias)` to return `True` if deleted, `False` if the alias did not exist.

*   Define `ModelDTO` in `clients/client-python/gravitino/dto/model_dto.py`:
    *   Implement methods `name()`, `comment()`, `properties()`, `latest_version()`, `audit_info()`.
    *   Ensure it deserializes from JSON with fields: 'name', 'comment', 'properties', 'latestVersion', 'audit'.

*   Define `ModelVersionDTO` in `clients/client-python/gravitino/dto/model_version_dto.py`:
    *   Implement methods `version()`, `uri()`, `aliases()`, `comment()`, `properties()`, `audit_info()`.
    *   Ensure it deserializes from JSON with fields: 'version', 'uri', 'aliases', 'comment', 'properties', 'audit'.

*   Implement `ModelResponse` in `clients/client-python/gravitino/dto/responses/model_response.py`:
    *   Ensure it deserializes from JSON with a top-level 'model' key.
    *   Implement `model()` to return `ModelDTO`.
    *   Implement `validate()` to raise `IllegalArgumentException` if required fields are missing.

*   Implement `ModelVersionListResponse` in `clients/client-python/gravitino/dto/responses/model_version_list_response.py`:
    *   Ensure it deserializes from JSON with a top-level 'versions' key.
    *   Implement `versions()` to return a list of integers.
    *   Implement `validate()` to raise `IllegalArgumentException` if 'versions' is absent or null.

*   Implement `ModelVersionResponse` in `clients/client-python/gravitino/dto/responses/model_vesion_response.py`:
    *   Ensure it deserializes from JSON with a top-level 'modelVersion' key.
    *   Implement `model_version()` to return `ModelVersionDTO`.
    *   Implement `validate()` to raise `IllegalArgumentException` if required fields are missing.

*   Ensure `Catalog.Type` includes a `MODEL` enum value for `GenericModelCatalog` instantiation.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.