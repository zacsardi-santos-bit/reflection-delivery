Implement the necessary changes to ensure correct prefix access control management in Ozone's high-availability mode. Update the in-memory prefix tree and transaction log index consistently, handle cleanup and validation issues, and ensure proper path validation for prefix ACL operations.

*   Update the in-memory prefix tree and metadata table:
    *   Always update these with the new ACL list and transaction log index as updateID for add, set, and remove operations, even if the ACL content hasn't changed.
    *   Ensure the returned status is OK for add and set operations.
    *   For remove operations, update the updateID even if the ACL is not found.

*   Handle cleanup of prefix entries:
    *   When the last ACL is removed, delete the prefix entry from both the in-memory prefix tree and the metadata table.
    *   Ensure retrieval of prefix info returns null and the ACL list is empty after deletion.

*   Validate prefix paths:
    *   Reject paths without a trailing slash with PREFIX_NOT_FOUND status.
    *   Reject paths with invalid structures (e.g., consecutive delimiters) with INVALID_PATH_IN_ACL_REQUEST status.

*   Implement method and class updates:
    *   Update `PrefixManagerImpl.getPrefixInfo(OzoneObj obj)` to return the `OmPrefixInfo` for the given prefix or null if not found.
    *   Ensure `OmPrefixInfo` equality includes objectID and updateID, and serialization/deserialization preserves these fields.
    *   Modify `OMPrefixAclResponse.addToDBBatch` to persist or delete prefix entries based on ACL list content.
    *   Update `OMPrefixAddAclRequest`, `OMPrefixRemoveAclRequest`, and `OMPrefixSetAclRequest` methods to validate paths and update caches correctly.

*   Ensure `OMRequestTestUtils` provides utility methods for testing:
    *   Implement methods to add prefixes to the table and create `OmPrefixInfo` instances for testing purposes.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.