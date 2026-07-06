Implement a new lightweight class to replace the existing heavyweight wrapper in the Recon key listing endpoint. Ensure that the new class captures only essential fields needed for the listing API and is used consistently throughout the codebase.

*   Create a new class `ReconBasicOmKeyInfo` in the package `org.apache.hadoop.ozone.recon.api.types`.
    *   Replace the `KeyEntityInfoProtoWrapper` class with `ReconBasicOmKeyInfo`.
    *   Implement the following methods in `ReconBasicOmKeyInfo`:
        *   `getPath()` returning a `String`.
        *   `getKey()` returning a `String`.
        *   `setKey(String key)` and `setPath(String path)` that throw `IllegalStateException` if `getKey()` or `getPath()` is called before setting.
        *   `getReplicationConfig()` returning a `ReplicationConfig` object.
        *   `getReplicatedSize()`, `getSize()`, `getParentId()`, `getCreationTime()`, `getModificationTime()` returning `long`.
        *   `getVolumeName()`, `getBucketName()`, `getKeyName()` returning `String`.
        *   `getIsKey()` returning a `boolean`.
        *   `getCodec()` as a static method returning `Codec<ReconBasicOmKeyInfo>`.

*   Update `ListKeysResponse` class in `org.apache.hadoop.ozone.recon.api.types`.
    *   Modify `getKeys()` to return `List<ReconBasicOmKeyInfo>`.
    *   Modify `setKeys()` to accept `List<ReconBasicOmKeyInfo>`.

*   Update the `ReconOMMetadataManager` interface in `org.apache.hadoop.ozone.recon.recovery`.
    *   Rename `getKeyTableLite` method to `getKeyTableBasic`.
    *   Ensure `getKeyTableBasic(BucketLayout bucketLayout)` returns `Table<String, ReconBasicOmKeyInfo>`.

*   Modify the `OMDBInsightEndpoint` to use `ReconBasicOmKeyInfo`.
    *   Ensure iteration, filtering, and computation of totals use `ReconBasicOmKeyInfo`.
    *   Maintain correct API response paths, keys, and replication information.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.