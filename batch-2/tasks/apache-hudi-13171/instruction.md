Implement a production-quality, engine-independent Avro reader context for Hudi's file group reading infrastructure. Ensure it supports field value lookup by name, record key extraction, and merging of skeleton and base file iterators. Update the abstract base class for file group reader tests to utilize the table metadata client.

Requirements:

*   Implement `HoodieAvroReaderContext` in `org.apache.hudi.avro` within the `hudi-common` module.
    *   Extend `HoodieReaderContext<IndexedRecord>`.
    *   Provide a constructor: `HoodieAvroReaderContext(StorageConfiguration<?> storageConfiguration, HoodieTableConfig tableConfig)`.
        *   If `tableConfig.populateMetaFields()` is true, `getRecordKey` must read from the metadata field at schema position 2 (`_hoodie_record_key`).
        *   If false, delegate to an automatically-built key generator.
    *   Provide a constructor: `HoodieAvroReaderContext(StorageConfiguration<?> storageConfiguration, HoodieTableConfig tableConfig, BaseKeyGenerator keyGenerator)`.
        *   If `tableConfig.populateMetaFields()` is false, delegate to the provided key generator using `keyGenerator.getRecordKey((GenericRecord) record)`.
    *   Implement `getValue(IndexedRecord record, Schema schema, String fieldName)` to return the field value or null if the field is absent.
    *   Implement `getRecordKey(IndexedRecord record, Schema schema)` to return a `String`.
        *   Use `_hoodie_record_key` if `populateMetaFields` is true.
        *   Use `keyGenerator.getRecordKey((GenericRecord) record)` if false.
    *   Implement `mergeBootstrapReaders(ClosableIterator<IndexedRecord> skeletonFileIterator, Schema skeletonRequiredSchema, ClosableIterator<IndexedRecord> dataFileIterator, Schema dataRequiredSchema)`.
        *   Return a `ClosableIterator<IndexedRecord>` combining skeleton and base records.
        *   Use `AvroSchemaUtils.mergeSchemas(skeletonRequiredSchema, dataRequiredSchema)` for schema, with skeleton fields first.
        *   Ensure equal-sized iterators produce the same number of merged records.
        *   Return an empty iterator for empty inputs.
        *   Throw `IllegalStateException` for mismatched iterator sizes.

*   Update `getHoodieReaderContext` in `TestHoodieFileGroupReaderBase` to accept a `HoodieTableMetaClient` parameter.
    *   Signature: `HoodieReaderContext<T> getHoodieReaderContext(String tablePath, Schema avroSchema, StorageConfiguration<?> storageConf, HoodieTableMetaClient metaClient)`.
    *   Update all subclass implementations (Java, Hive, Spark) accordingly.

*   Modify `HoodieFileGroupReaderTestHarness`.
    *   Make `shouldWritePositions` and `readerContext` instance variables.
    *   Implement `TestKeyGenerator` as a `BaseKeyGenerator`.
        *   `getRecordKey(GenericRecord record)` returns `record.get(ROW_KEY).toString()`.
        *   `getPartitionPath(GenericRecord record)` returns an empty string.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.