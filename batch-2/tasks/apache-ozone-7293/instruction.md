Implement the container diff logic in Apache Ozone to compare a datanode's local container checksum tree against a peer's tree. Generate a report detailing missing and corrupt data from the local perspective, and track relevant metrics for the operation.

*   Create the `ContainerDiffReport` class in the `org.apache.hadoop.ozone.container.checksum` package:
    *   Implement a default constructor.
    *   Implement methods: `addMissingBlock(BlockMerkleTree)`, `addMissingChunk(long blockId, ChunkMerkleTree)`, `addCorruptChunk(long blockId, ChunkMerkleTree)`.
    *   Implement methods to retrieve discrepancies: `getMissingBlocks()`, `getMissingChunks()`, `getCorruptChunks()`.
    *   Implement `needsRepair()` to return true if any discrepancies exist.

*   Implement the `diff` method in `ContainerChecksumTreeManager`:
    *   Signature: `ContainerDiffReport diff(KeyValueContainerData thisContainer, ContainerProtos.ContainerChecksumInfo peerChecksumInfo) throws StorageContainerException`.
    *   Throw `StorageContainerException` if no local checksum file exists or container IDs do not match.
    *   Return a `ContainerDiffReport` with `needsRepair()` false when trees are identical, and increment the no-repair counter.
    *   Add missing blocks and chunks to the report if they exist in the peer's tree but not locally, set `needsRepair()` to true, and increment the repair counter.
    *   Exclude differences where the peer's tree is missing data.
    *   Add corrupt chunks to the report when local checksums differ from the peer's, and the local copy is unhealthy while the peer's is healthy.
    *   Exclude blocks marked as deleted in either local or peer's checksum info from the diff.

*   Track metrics for the diff operation:
    *   Record execution latency using `getMerkleTreeDiffLatencyNS()`.
    *   Increment `getMerkleTreeDiffFailure()` on exceptions, and throw `StorageContainerException`.

*   Update `ContainerMerkleTreeMetrics` with new methods:
    *   `getMerkleTreeDiffLatencyNS()`, `getNoRepairContainerDiffs()`, `getRepairContainerDiffs()`, `getMerkleTreeDiffFailure()`.
    *   Implement increment methods: `incrementMerkleTreeDiffFailures()`, `incrementNoRepairContainerDiffs()`, `incrementRepairContainerDiffs()`.

*   Modify `ChunkMerkleTree` protobuf message:
    *   Add optional `bool isHealthy` field at number 4, defaulting to true for all chunks.

*   Add `CONTAINER_ID_MISMATCH` to the `Result` enum in protobuf:
    *   Assign value 48 for container ID mismatch errors during diff operations.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.