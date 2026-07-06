Reorganize the package structure for specific classes to better reflect their dependencies and responsibilities. Simplify the constructor API for the HBase HFile reader to eliminate the need for explicit cache configuration.

*   Move the `HFileBootstrapIndex` class:
    *   Relocate to the package `org.apache.hudi.common.bootstrap.index.hfile`.
    *   Ensure the file path is `hudi-hadoop-common/src/main/java/org/apache/hudi/common/bootstrap/index/hfile/HFileBootstrapIndex.java`.

*   Move the `HoodieHFileUtils` class:
    *   Relocate to the package `org.apache.hudi.io.hadoop`.
    *   Ensure the file path is `hudi-hadoop-common/src/main/java/org/apache/hudi/io/hadoop/HoodieHFileUtils.java`.

*   Move the `HoodieHBaseAvroHFileReader` class:
    *   Relocate to the package `org.apache.hudi.io.hadoop`.
    *   Ensure the file path is `hudi-hadoop-common/src/main/java/org/apache/hudi/io/hadoop/HoodieHBaseAvroHFileReader.java`.

*   Update the `HoodieHBaseAvroHFileReader` constructors:
    *   Implement a constructor `HoodieHBaseAvroHFileReader(StorageConfiguration<?> storageConf, StoragePath path, Option<Schema> schemaOpt)` that initializes the reader without requiring a `CacheConfig` parameter.
    *   Implement a constructor `HoodieHBaseAvroHFileReader(StorageConfiguration<?> storageConf, StoragePath path, HoodieStorage storage, byte[] content, Option<Schema> schemaOpt)` that initializes the reader from in-memory byte content without requiring a `CacheConfig` parameter.

*   Update all import statements:
    *   Ensure all callers of `HFileBootstrapIndex`, `HoodieHFileUtils`, and `HoodieHBaseAvroHFileReader` import these classes from their new package locations.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.