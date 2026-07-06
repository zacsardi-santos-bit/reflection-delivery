I'm hitting a bug in the governance module's end-of-block proposal processing.

*   When EndBlocker iterates the active proposals queue and encounters a proposal that cannot be decoded (i.e., retrieving it from the store returns a collections encoding error), it must not panic and must return nil so the chain is not halted.

*   When EndBlocker processes an undecodable active proposal, it must remove the queue entry from the active proposals queue. The queue entry must no longer be present after EndBlocker returns.

*   When EndBlocker iterates the inactive proposals queue and encounters a proposal that cannot be decoded (i.e., retrieving it returns a collections encoding error), it must return nil and must remove the queue entry from the inactive proposals queue so the entry is not re-encountered on subsequent blocks.

*   After EndBlocker removes undecodable proposals from their respective queues, a subsequent call to EndBlocker with the same context must also succeed and return nil (there is nothing left to re-process).

*   The queue entry removal for an undecodable proposal must use the key obtained directly from the queue walk callback, not from any field of the (partially decoded or zero-value) proposal struct, since those fields may be nil or invalid for an undecodable proposal.


*   Interface details: Type: Function
Name: EndBlocker
Location: x/gov/abci.go
Signature: EndBlocker(ctx sdk.Context, keeper *keeper.Keeper) error
Description: Processes expired governance proposals at the end of each block. Must handle proposals that return a collections encoding error gracefully — without panicking and without returning an error — and must remove the corresponding entry from the active or inactive proposals queue using the walk iteration key (not any field from the undecodable proposal itself).


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.