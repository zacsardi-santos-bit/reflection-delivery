Implement a mechanism to record deleted block IDs in a container's checksum tree file after block deletion. Ensure the checksum file reflects all deletions accurately, even if deletions are retried. Refactor existing code to improve configuration handling and utility functions for testing.

*   Update `ContainerChecksumTreeManager`:
    *   Change the constructor to accept `ConfigurationSource` instead of `DatanodeConfiguration`.
    *   Implement a static method `getContainerChecksumFile(ContainerData data)` that returns a `File` for the checksum file named `<containerID>.tree` located in the container's metadata path.
    *   Modify `markBlocksAsDeleted` to accept `Collection<Long>` for deleted block IDs, ensuring the list is sorted and deduplicated before writing to the checksum file.
    *   Ensure multiple calls to `markBlocksAsDeleted` accumulate deletions correctly, maintaining a sorted and deduplicated list.

*   Update `BlockDeletingService`:
    *   Add a `ContainerChecksumTreeManager` parameter to the constructor, positioned between `threadNamePrefix` and `reconfigurationHandler`.
    *   After deleting blocks, call `markBlocksAsDeleted` to update the checksum file with deleted block IDs before removing deletion transactions from the database.

*   Refactor `ContainerData`:
    *   Declare `getMetadataPath()` as an abstract method returning a `String`.

*   Create `ContainerMerkleTreeTestUtils` in the `org.apache.hadoop.ozone.container.checksum` package:
    *   Implement `assertTreesSortedAndMatch` to verify checksum trees are sorted and match.
    *   Implement `buildChunk` to build chunk data using configuration for chunk size and bytes-per-checksum.
    *   Implement `readChecksumFile` to read and parse the checksum file using `ContainerChecksumTreeManager.getContainerChecksumFile`.

*   Ensure the checksum file update occurs even if block files are missing during a retry, and that the file accurately reflects all deletions in sorted, deduplicated order.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.