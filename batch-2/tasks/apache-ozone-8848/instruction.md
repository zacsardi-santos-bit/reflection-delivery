Implement a mechanism to schedule unused pre-allocated storage blocks for deletion in an Ozone cluster during commit operations. Update response objects to properly track and expose keys and block lists queued for cleanup, ensuring all entries are persisted to the deleted table.

*   Identify and schedule unused pre-allocated blocks for deletion:
    *   Calculate uncommitted blocks as the difference between allocated and committed blocks during key commit operations.
*   Update key overwrite handling:
    *   For regular key overwrites with uncommitted blocks, ensure the keys-to-delete map in the commit response contains exactly 1 entry with 2 OmKeyInfo objects (overwritten key and uncommitted pseudo-key).
    *   Use a pseudo-object-ID derived from the transaction log index for delete table keys, ensuring uniqueness and avoiding the objectID of any OmKeyInfo.
*   Modify multipart upload part commit handling:
    *   When committing a part with uncommitted blocks but no overwrite, ensure `getKeyToDelete()` returns 1 entry with 1 OmKeyInfo for uncommitted blocks.
    *   For overwrites without uncommitted blocks, ensure `getKeyToDelete()` returns 1 entry for the original part's blocks.
    *   For overwrites with uncommitted blocks, ensure `getKeyToDelete()` returns 2 separate entries: one for the overwritten part's blocks and one for uncommitted pseudo-key blocks.
*   Update class constructors and methods:
    *   In `S3MultipartUploadCommitPartResponse`, change the constructor to accept `Map<String, RepeatedOmKeyInfo>` for keys to delete, replacing the `PartKeyInfo` parameter.
    *   Add `getKeyToDelete()` method to return the map of keys scheduled for deletion.
    *   In `S3MultipartUploadCommitPartResponseWithFSO`, update the constructor to match the signature change of the base class.
*   Ensure persistence of deletion entries:
    *   Persist all entries in the keys-to-delete map to the `deletedTable` during the database batch write operation, with each entry written as a separate record keyed by its delete path key.
    *   After committing a key overwrite with uncommitted blocks, ensure the `deletedTable` contains an entry with exactly 2 OmKeyInfo objects for both the overwritten version and the uncommitted pseudo-key.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.