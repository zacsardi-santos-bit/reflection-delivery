Implement methods to address a data-corruption bug in the record layer's handling of uncommitted versionstamp data during bulk deletions. Ensure that any pending uncommitted mutations are canceled when records are deleted, preventing stale data from being committed.

*   Implement `clear(byte[] key)` in `FDBRecordContext`:
    *   Clear a single key from the underlying database transaction.
    *   Remove any uncommitted versionstamp mutation and local version cache entry associated with that key.
    *   Ensure the method is callable directly on an `FDBRecordContext` instance.

*   Implement `clear(Range range)` in `FDBRecordContext`:
    *   Clear a key range from the underlying database transaction.
    *   Remove all uncommitted versionstamp mutations and local version cache entries within that range.
    *   Ensure the method is callable directly on an `FDBRecordContext` instance.

*   Ensure `deleteRecordsWhere()`:
    *   Removes all uncommitted versionstamp mutations for version index entries in the matching prefix range.
    *   After execution, `loadRecordVersion()` returns `Optional.empty()` for each deleted primary key.
    *   `scanIndexRecords()` on any version index returns no entries for the deleted group prefix.

*   Ensure `deleteRecordsWhere()` with a MAX_EVER_VERSION index:
    *   Removes the index entry for the deleted group.
    *   Retains correct maximum version entries for non-deleted groups.

*   Ensure `deleteAllRecords()`:
    *   Clears all uncommitted versionstamp mutations for all version indexes and local version cache entries.
    *   After execution, `loadRecordVersion()` returns `Optional.empty()` for every record key.
    *   All version index scans return empty results.

*   Ensure `FDBRecordStore.deleteStore()` or equivalent keyspace path operation:
    *   Clears all uncommitted versionstamp mutations for the store's version-indexed data.
    *   After execution, `loadRecordVersion()` returns `Optional.empty()` for keys in the deleted store.
    *   Index scans on a freshly-created store at that path return empty results.
    *   Records in other stores sharing the same transaction remain unaffected.

*   Ensure `markIndexDisabled()`:
    *   Removes pending uncommitted versionstamp entries for the disabled index.
    *   After execution, scanning the disabled index throws `ScanNonReadableIndexException`.
    *   The underlying index subspace is empty.
    *   `loadRecordVersion()` returns the correct version for previously committed records.

*   Ensure version index removal from record metadata:
    *   Clears all uncommitted versionstamp mutations for the removed index.
    *   After reopening the store, scanning the removed index throws `MetaDataException`.
    *   The removed index's underlying subspace is empty.
    *   Other version indexes and `loadRecordVersion()` results are unaffected.
    *   A newly saved record's version is still retrievable via `loadRecordVersion()`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.