Generalize the metadata index infrastructure by renaming specific classes and methods to support multiple index types, including a new secondary index type. Implement a unified metadata client accessor and ensure consistent behavior for functional and secondary index partitions.

*   Rename classes in the `org.apache.hudi.common.model` package:
    *   Change `HoodieFunctionalIndexDefinition` to `HoodieIndexDefinition`.
        *   Ensure the constructor signature remains: `(String indexName, String indexType, String indexFunction, List<String> sourceFields, Object options)`.
        *   Maintain the `getIndexName()` method.
    *   Change `HoodieFunctionalIndexMetadata` to `HoodieIndexMetadata`.
        *   Ensure the constructor signature remains: `(Map<String, HoodieIndexDefinition> indexDefinitions)`.
        *   Maintain the `getIndexDefinitions()` method returning a map.

*   Update `HoodieTableMetaClient` in the `hudi-common/src/main/java/org/apache/hudi/common/table` package:
    *   Replace `getFunctionalIndexMetadata()` with `getIndexMetadata()`.
        *   Ensure it returns `Option<HoodieIndexMetadata>`.

*   Modify `HoodieIndexUtils`:
    *   Ensure `getPartitionNameFromPartitionType` calls `metaClient.getIndexMetadata()`.
    *   For `FUNCTIONAL_INDEX` partition type, throw `IllegalArgumentException` if `getIndexMetadata()` returns an empty `Option`.

*   Enhance `MetadataPartitionType` enum in the `hudi-common/src/main/java/org/apache/hudi/metadata` package:
    *   Add a new `SECONDARY_INDEX` constant.
    *   In `getEnabledPartitions()`, treat `SECONDARY_INDEX` the same as `FUNCTIONAL_INDEX`:
        *   When enabled by configuration but not initialized, return a list containing only `FILES`.

*   Implement `fromPartitionPath` static method in `MetadataPartitionType`:
    *   Map partition paths to enum values:
        *   "files" -> `FILES`
        *   Paths starting with "func_index_" -> `FUNCTIONAL_INDEX`
        *   Paths starting with "secondary_index_" -> `SECONDARY_INDEX`
        *   "column_stats" -> `COLUMN_STATS`
        *   "bloom_filters" -> `BLOOM_FILTERS`
        *   "record_index" -> `RECORD_INDEX`
        *   "partition_stats" -> `PARTITION_STATS`
        *   Throw `IllegalArgumentException` for unrecognized paths.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.