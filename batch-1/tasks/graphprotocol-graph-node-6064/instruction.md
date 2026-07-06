Implement a method to retrieve a block's number and metadata, including a timestamp in raw Unix seconds, by its hash. Rename the method to clearly reflect its functionality and ensure the timestamp is returned as a plain unsigned integer.

*   Update the ChainStore trait in `graph/src/components/store/traits.rs`:
    *   Replace the `block_pointer` method with `block_number`.
    *   Method signature: `async fn block_number(&self, hash: &BlockHash) -> Result<Option<(String, BlockNumber, Option<u64>, Option<BlockHash>)>, StoreError>`.
    *   Ensure the method returns:
        *   The network name as a `String`.
        *   The block number as `BlockNumber`.
        *   The timestamp as `Option<u64>`, representing raw Unix seconds.
            *   Return `Some(1657712166u64)` for a timestamp value of 1657712166.
            *   Return `None` if the block has no timestamp.
        *   The parent block hash as `Option<BlockHash>`.

*   Implement the `block_number` method in the ChainStore implementation located at `store/postgres/src/chain_store.rs`:
    *   Ensure it matches the signature: `async fn block_number(&self, hash: &BlockHash) -> Result<Option<(String, BlockNumber, Option<u64>, Option<BlockHash>)>, StoreError>`.
    *   Return the timestamp as `Option<u64>` directly, without any internal time abstraction.
    *   Handle cases where the block has no timestamp by returning `None` for the timestamp field.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.