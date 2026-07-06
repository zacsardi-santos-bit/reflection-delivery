Implement support for vended storage credentials in Iceberg's file I/O layer. Introduce a new interface for file I/O implementations to receive storage credentials, and update relevant classes to handle these credentials appropriately.

*   Create a new interface `StorageCredential` in `org.apache.iceberg.io`.
    *   Include a static factory method `create(String prefix, Map<String, String> config)` returning a `StorageCredential`.
    *   Define accessor methods `String prefix()` and `Map<String, String> config()`.
    *   Ensure the implementation supports both Kryo and Java serialization and implements `equals` and `hashCode`.
    *   Throw `IllegalArgumentException` with the message 'Invalid prefix: must be non-empty' if the prefix is empty.
    *   Throw `IllegalArgumentException` with the message 'Invalid config: must be non-empty' if the config map is empty.
    *   Implement an Immutables-style builder pattern with `ImmutableStorageCredential.builder().prefix(String).config(Map).build()`.
*   Create a new interface `SupportsStorageCredentials` in `org.apache.iceberg.io`.
    *   Include methods `void setCredentials(List<StorageCredential> credentials)` and `List<StorageCredential> credentials()`.
*   Update `CatalogUtil` in `org.apache.iceberg` to add a new overload of `loadFileIO`.
    *   Accept a fourth parameter `List<StorageCredential> storageCredentials`.
    *   If the `FileIO` instance implements `SupportsStorageCredentials`, call `setCredentials(storageCredentials)` before `initialize(properties)`.
*   Modify `S3FileIO` in `aws/src/main/java/org/apache/iceberg/aws/s3/S3FileIO.java`.
    *   Implement `SupportsStorageCredentials`.
    *   Ensure credentials are applied during `initialize()` to override S3 properties.
    *   Throw `IllegalStateException` with the message 'Invalid S3 Credentials: only one S3 credential should exist' if more than one S3 credential is provided.
*   Modify `GCSFileIO` in `gcp/src/main/java/org/apache/iceberg/gcp/gcs/GCSFileIO.java`.
    *   Implement `SupportsStorageCredentials`.
    *   Ensure credentials are applied during `initialize()` to override GCS properties.
    *   Throw `IllegalStateException` with the message 'Invalid GCS Credentials: only one GCS credential should exist' if more than one GCS credential is provided.
*   Modify `ResolvingFileIO` in `core/src/main/java/org/apache/iceberg/io/ResolvingFileIO.java`.
    *   Implement `SupportsStorageCredentials`.
    *   Propagate stored credentials to delegate `FileIO` instances it loads for each path scheme.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.