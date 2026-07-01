Refactor the distributed transaction commit handling code in Apache Geode to improve testability by isolating key behaviors related to transaction tracking and member-departure recovery. Implement the following changes to ensure these behaviors can be independently verified without requiring a live distributed system.

*   Implement a zero-argument constructor in `TXCommitMessage` to allow instantiation without parameters.

*   Refactor `TXCommitMessage`:
    *   Implement `isProcessing()` to return a boolean indicating if the transaction is currently being processed.
    *   Implement `setDontProcess()` to mark a transaction as not to be processed.
    *   Ensure the following methods are overridable: `getDistributionManager()`, `getSender()`, `createReplyProcessor()`, `getFarSiders()`, and `createQueryMessage(CommitProcessQueryReplyProcessor)`.

*   Update `TXCommitMessage.memberDeparted`:
    *   Signature: `memberDeparted(DistributionManager distributionManager, InternalDistributedMember id, boolean crashed) -> void`.
    *   Ensure it calls `createReplyProcessor()` and `createQueryMessage(processor)`, sends the query message via `distributionManager.putOutgoing()`, and calls `waitForRepliesUninterruptibly()` on the reply processor when `isProcessing()` is false and `getFarSiders()` is non-empty.

*   Ensure `TXCommitMessage.CommitProcessQueryReplyProcessor` and `TXCommitMessage.CommitProcessQueryMessage` are accessible inner classes of `TXCommitMessage`.

*   Implement `TXFarSideCMTracker`:
    *   Constructor: `TXFarSideCMTracker(int historySize)`.
    *   Implement `foundTxInProgress(TXCommitMessage message)` to return true if the message is non-null and `message.isProcessing()` is true; otherwise, return false.
    *   Implement `getTxInProgress()` to return the internal map of transactions currently in progress.
    *   Implement `foundFromHistory(Object key)` to return true if the key exists in the history of processed transactions.

*   Update `TXFarSideCMTracker.commitProcessReceived`:
    *   Return true if the message for the key is in progress or found in history.
    *   Return false and call `setDontProcess()` on the message if found but not in progress and not in history.
    *   Return false without exception if no message is found for the key and not in history.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.