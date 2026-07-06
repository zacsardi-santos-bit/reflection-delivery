Implement a new LSM-tree-based storage engine for the IoTeX blockchain's key-value store layer. Ensure it can be seamlessly swapped with the existing backend without altering application-level read/write logic. Follow the specified behaviors and method signatures to ensure compatibility and functionality.

*   Implement the `NewPebbleDB` function in `db/db_pebble.go`:
    *   Accept a `Config` struct with a `DbPath` string field.
    *   Return a `PebbleDB` instance.

*   Implement the `PebbleDB` class in `db/db_pebble.go` with the following methods:
    *   `Start(ctx context.Context) error`
        *   Open or create the database at the configured path.
        *   Return an error if the database cannot be opened.
    *   `Stop(ctx context.Context) error`
        *   Close the database.
        *   Return an error if the database cannot be closed cleanly.
    *   `Get(ns string, key []byte) ([]byte, error)`
        *   Retrieve the value for the given namespace and key.
        *   Return an error wrapping `ErrNotExist` if the key does not exist.
    *   `Put(ns string, key []byte, value []byte) error`
        *   Store a key-value pair in the given namespace.
        *   Create the namespace if it does not exist and overwrite any existing value.
    *   `Delete(ns string, key []byte) error`
        *   Remove the key from the namespace.
        *   Ensure idempotency: return nil if the key does not exist.
    *   `WriteBatch(kvsb batch.KVStoreBatch) error`
        *   Apply all operations in the batch atomically.
        *   Ensure last-write-wins deduplication for duplicate namespace+key entries.
    *   `Filter(ns string, cond Condition, minKey []byte, maxKey []byte) ([][]byte, [][]byte, error)`
        *   Return key-value pairs in the namespace satisfying the condition and key range.
        *   Return `ErrNotExist` if no matches are found or the namespace does not exist.
    *   `ForEach(ns string, fn func(k, v []byte) error) error`
        *   Iterate over all key-value pairs in the namespace.
        *   Return nil if the namespace is empty or does not exist.

*   Ensure:
    *   Keys from one namespace are not visible in another namespace.
    *   After `WriteBatch`, deleted keys return `ErrNotExist` and written keys return the expected value.
    *   The `Filter` method handles nil `minKey` and `maxKey` appropriately for no bounds.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.