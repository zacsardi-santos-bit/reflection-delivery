Implement a two-phase cleanup protocol for collection version histories by adding methods to mark versions for deletion and to permanently delete them. Ensure these operations handle multiple collections per request and report individual collection outcomes. Extend interfaces and update protobuf messages to support these functionalities.

*   Implement the `DeleteCollectionVersion` method in `go/pkg/sysdb/coordinator/table_catalog.go`:
    *   Accept a request with a list of version groups per collection.
    *   Remove specified versions from the version history file in S3.
    *   Write a new version file and update the database record atomically.
    *   Return a response with a `CollectionIdToSuccess` map indicating per-collection success.
    *   Ensure no top-level error is returned if a collection does not exist; set `CollectionIdToSuccess[collectionID] = false` instead.

*   Implement the `MarkVersionForDeletion` method in `go/pkg/sysdb/coordinator/table_catalog.go`:
    *   Accept a request similar to `DeleteCollectionVersion`.
    *   Mark specified versions with `marked_for_deletion = true` without removing them.
    *   Write a new version file and update the database atomically.
    *   Return a response with a `CollectionIdToSuccess` map.
    *   Ensure no top-level error is returned if a collection does not exist or versions are not present; set `CollectionIdToSuccess[collectionID] = false`.

*   Implement the `GetVersionFileNamesForCollection` method in `go/pkg/sysdb/coordinator/table_catalog.go`:
    *   Accept a context, tenant ID, and collection ID.
    *   Return the current version file name from the database.

*   Extend the `S3MetaStoreInterface` with the `DeleteVersionFile` method in `go/pkg/sysdb/metastore/s3/impl.go`:
    *   Implement deletion of a version file from S3 using tenant ID, collection ID, and file name.

*   Extend the `ICollectionDb` interface with the `UpdateVersionFileName` method in `go/pkg/sysdb/metastore/db/dbmodel/collection.go`:
    *   Perform a compare-and-swap update on the version file name.
    *   Return the number of rows affected and any error.
    *   Ensure the mock in `go/pkg/sysdb/metastore/db/dbmodel/mocks/ICollectionDb.go` implements this method.

*   Update protobuf messages in `idl/chromadb/proto/coordinator.proto`:
    *   Add `VersionListForCollection`, `MarkVersionForDeletionRequest`, `MarkVersionForDeletionResponse`, `DeleteCollectionVersionRequest`, and `DeleteCollectionVersionResponse`.
    *   Extend `CollectionVersionInfo` with `version_file_name` (field 6) and `marked_for_deletion` (field 7).

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.