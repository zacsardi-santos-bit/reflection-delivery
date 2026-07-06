Move the database-specific cursor implementations from the core trie library to the database integration package. Ensure these types are publicly accessible from the new location and update any existing code to import them from the correct package.

*   Relocate the following types from the core trie library to the database integration package:
    *   Move `DatabaseHashedCursorFactory`, `DatabaseHashedAccountCursor`, and `DatabaseHashedStorageCursor` from `reth_trie::hashed_cursor` to `reth_trie_db`.
        *   Implement these in `crates/trie/db/src/hashed_cursor.rs`.
        *   Re-export them as `reth_trie_db::DatabaseHashedCursorFactory`, `reth_trie_db::DatabaseHashedAccountCursor`, and `reth_trie_db::DatabaseHashedStorageCursor`.
    *   Move `DatabaseTrieCursorFactory`, `DatabaseAccountTrieCursor`, and `DatabaseStorageTrieCursor` from `reth_trie::trie_cursor` to `reth_trie_db`.
        *   Implement these in `crates/trie/db/src/trie_cursor.rs`.
        *   Re-export them as `reth_trie_db::DatabaseTrieCursorFactory`, `reth_trie_db::DatabaseAccountTrieCursor`, and `reth_trie_db::DatabaseStorageTrieCursor`.

*   Update module declarations and exports:
    *   Declare `mod hashed_cursor` in `crates/trie/db/src/lib.rs` and re-export the three types.
    *   Declare `mod trie_cursor` in `crates/trie/db/src/lib.rs` and re-export the three types.
    *   Remove the re-exports of these types from `reth_trie::hashed_cursor` and `reth_trie::trie_cursor`.

*   Ensure functionality and behavior:
    *   Implement `DatabaseHashedCursorFactory` with the signature `new(tx: &'a TX) -> DatabaseHashedCursorFactory<'a, TX>`.
    *   Implement `DatabaseAccountTrieCursor::new(cursor: C) -> DatabaseAccountTrieCursor<C>` and `DatabaseStorageTrieCursor::new(cursor: C, hashed_address: B256) -> DatabaseStorageTrieCursor<C>`.
    *   Ensure `DatabaseAccountTrieCursor` and `DatabaseStorageTrieCursor` implement the `TrieCursor` trait.
    *   Verify `DatabaseHashedAccountCursor` iterates accounts in sorted order, prioritizing post-state values and excluding deleted entries.
    *   Ensure `DatabaseHashedStorageCursor` correctly reports storage as empty or non-empty based on database and post-state conditions.
    *   Implement logic for storage cursor to merge entries in sorted order, excluding zero-value post-state entries.
    *   Confirm `TrieWalker` behavior with `walker.can_skip_current_node` based on changed prefix set and root hash presence.

*   Update tests:
    *   Move integration tests requiring a database to the database integration package.
    *   Update import paths in tests, such as changing `reth_trie::trie_cursor::DatabaseTrieCursorFactory` to `reth_trie_db::DatabaseTrieCursorFactory`.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.