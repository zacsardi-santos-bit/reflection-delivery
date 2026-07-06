Implement a generic data model mapper for the Qdrant connector to support dictionary-based records. Fix two bugs in the Azure AI Search generic data model mapper related to handling partial records and exception types.

*   Create `QdrantGenericDataModelMapper` in the `Microsoft.SemanticKernel.Connectors.Qdrant` namespace.
    *   Implement `IVectorStoreRecordMapper<VectorStoreGenericDataModel<ulong>, PointStruct>` and `IVectorStoreRecordMapper<VectorStoreGenericDataModel<Guid>, PointStruct>`.
    *   Constructor must accept `VectorStoreRecordDefinition` and `bool hasNamedVectors`.

*   Implement `MapFromDataToStorageModel` in `QdrantGenericDataModelMapper`.
    *   Map `ulong` keys to `PointId.Num` and `Guid` keys to `PointId.Uuid` using 'D' format.
    *   Map supported data types to Qdrant payload `Value` entries, storing nulls as `NullValue`.
    *   Store vectors in `PointStruct.Vectors.Vector` if `hasNamedVectors` is false; otherwise, use `PointStruct.Vectors.Vectors_.Vectors` dictionary.
    *   Throw `VectorStoreRecordMappingException` with message: "Vector property '{propertyName}' on provided record of type VectorStoreGenericDataModel must be of type ReadOnlyMemory<float> and not null." if vector property is not `ReadOnlyMemory<float>`.
    *   Silently skip absent data properties in `dataModel.Data`.

*   Implement `MapFromStorageToDataModel` in `QdrantGenericDataModelMapper`.
    *   Map `PointId.Num` to `ulong` key and `PointId.Uuid` string to `Guid`.
    *   Map Qdrant payload values back to .NET types, handling nulls appropriately.
    *   Silently skip absent data properties in `PointStruct.Payload`.
    *   Silently skip absent vector properties in `PointStruct.Vectors` if `hasNamedVectors` is true.

*   Update `QdrantVectorStoreRecordCollection<TRecord>` to use `QdrantGenericDataModelMapper` automatically for `VectorStoreGenericDataModel<ulong>` or `VectorStoreGenericDataModel<Guid>`.

*   Update `AzureAISearchGenericDataModelMapper`.
    *   `MapFromStorageToDataModel` must throw `VectorStoreRecordMappingException` with message: "The key property '{keyPropertyName}' is missing from the record retrieved from storage." when the key is missing.
    *   `MapFromDataToStorageModel` must skip absent data and vector properties in `dataModel.Data` and `dataModel.Vectors`.
    *   `MapFromStorageToDataModel` must skip absent properties in the storage `JsonObject`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.