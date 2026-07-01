Implement a batch reservation feature for a bounded async multi-producer single-consumer (MPSC) channel. Provide both blocking and non-blocking methods to reserve multiple channel slots at once, ensuring proper handling of capacity and error conditions.

*   Implement the async method `reserve_many` in `tokio/src/sync/mpsc/bounded.rs`:
    *   Signature: `pub async fn reserve_many(&self, n: usize) -> Result<PermitIterator<'_, T>, SendError<()>>`
    *   Wait until `n` slots are available and reserve them atomically.
    *   Return `PermitIterator` on success.
    *   Return `Err(SendError(()))` if `n` exceeds the channel's original capacity, `Semaphore::MAX_PERMITS`, `usize::MAX`, or if the channel is closed.
    *   Return `Ok` with an empty iterator when `n == 0` and the channel is open, even if full.
    *   Return `Err` when `n == 0` and the channel is closed.

*   Implement the synchronous method `try_reserve_many` in `tokio/src/sync/mpsc/bounded.rs`:
    *   Signature: `pub fn try_reserve_many(&self, n: usize) -> Result<PermitIterator<'_, T>, TrySendError<()>>`
    *   Attempt to reserve `n` slots immediately without blocking.
    *   Return `PermitIterator` on success.
    *   Return `Err(TrySendError::Full(()))` if `n` exceeds the channel's original capacity, `Semaphore::MAX_PERMITS`, or `usize::MAX`.
    *   Return `Err(TrySendError::Closed(()))` if the channel is closed.
    *   Return `Ok` with an empty iterator when `n == 0` and the channel is open, even if full.
    *   Return `Err(TrySendError::Closed(()))` when `n == 0` and the channel is closed.

*   Define the `PermitIterator` struct in `tokio/src/sync/mpsc/bounded.rs`:
    *   Implement `Iterator<Item = Permit<'_, T>>`, `ExactSizeIterator`, and `FusedIterator`.
    *   Provide a `.len()` method returning the number of remaining unused permits.
    *   Each `Permit` yielded must have a `.send(value: T)` method to place the value in the channel.
    *   Dropping `PermitIterator` with unused permits must release all reserved capacity back to the channel and wake any waiting tasks.
    *   Re-export `PermitIterator` as `tokio::sync::mpsc::PermitIterator`.

*   Ensure reserving `n` permits reduces `tx.capacity()` by `n`, and each unused permit restores capacity when dropped.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.