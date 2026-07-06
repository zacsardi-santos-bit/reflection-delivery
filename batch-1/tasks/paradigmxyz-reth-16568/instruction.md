Implement a dedicated trait for fetching ommer (uncle) block headers, and ensure the database provider implements this trait. This will allow efficient retrieval of ommer headers by block number or block hash without loading the entire block.

*   Define the `OmmersProvider` trait:
    *   Location: `crates/storage/storage-api/src/ommers.rs`
    *   Extend `HeaderProvider + Send + Sync`.
    *   Include the method signature: `fn ommers(&self, id: BlockHashOrNumber) -> ProviderResult<Option<Vec<Self::Header>>>`.
    *   Re-export the trait from the storage-api crate (`crates/storage/storage-api/src/lib.rs`).

*   Implement the `OmmersProvider` trait for `DatabaseProvider`:
    *   Location: `crates/storage/provider/src/providers/database/provider.rs`
    *   Import `OmmersProvider` from `reth_storage_api`.
    *   Implement the `ommers` method to accept a `BlockHashOrNumber` and return `ProviderResult<Option<Vec<Self::Header>>>`.
    *   Ensure the method handles valid block numbers, such as 0, without panicking or returning an error.

*   Ensure the `DatabaseProvider` can retrieve ommer headers by block number or block hash:
    *   The `ommers` method must succeed and return a result, even if the block has no uncles or is past the Merge.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.