Implement chunk-level health tracking in the container Merkle tree system of Apache Ozone. Extend the functionality to include a health flag for chunks, support incremental updates from serialized trees, and register an on-demand scanner callback.

*   Modify `ContainerMerkleTreeWriter`:
    *   Update `addChunks(long blockID, boolean healthy, Collection<ContainerProtos.ChunkInfo> chunks)` to include a `boolean healthy` parameter. Ensure each `ChunkMerkleTree` entry has `isHealthy` set according to this parameter.
    *   Add a varargs overload `addChunks(long blockID, boolean healthy, ContainerProtos.ChunkInfo... chunks)` to accept multiple `ChunkInfo` instances directly.
    *   Introduce `addBlock(long blockID)` to insert an empty block into the tree without affecting existing entries.
    *   Implement a new constructor `ContainerMerkleTreeWriter(ContainerProtos.ContainerMerkleTree fromTree)` that initializes the writer from an existing proto. Ensure `toProto()` outputs identical to the input proto, including empty blocks.
    *   Ensure the writer remains mutable after initialization from a proto, allowing further updates with `addChunks` or `addBlock`.

*   Update the `ChunkMerkleTree` protobuf message:
    *   Add a `boolean isHealthy` field.
    *   Provide access via `getIsHealthy()` and `setIsHealthy(boolean)` methods.

*   Rename method in `KeyValueHandler`:
    *   Change `createContainerMerkleTree` to `createContainerMerkleTreeFromMetadata` to clarify it reads from metadata.
    *   Update all internal call sites to use the new method name.

*   Enhance `ContainerSet`:
    *   Add `registerContainerScanHandler(Function<Container, Optional<Future<?>>> scanHandler)` to allow registration of an on-demand scan handler.
    *   Ensure the handler is invoked automatically during reconciliation to trigger container scans.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.