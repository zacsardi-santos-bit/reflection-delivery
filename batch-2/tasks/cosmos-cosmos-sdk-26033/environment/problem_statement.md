## Description

The governance module's end-of-block proposal processing has a bug when it encounters proposals that are no longer decodable — for example, after a chain upgrade that removes a previously registered message type. When such a proposal is present in either the active or inactive voting queue, the block processor fails to clean it up properly, which can cause the chain to either panic or become stuck replaying the same broken queue entry on every subsequent block.

## Expected Behavior

- When an undecodable proposal is found in the active proposals queue, the block processor should handle it gracefully: mark it as failed, remove it from the active queue, and continue without panicking or returning an error.
- When an undecodable proposal is found in the inactive proposals queue, the processor should similarly remove the entry and continue without error.
- After the undecodable entry is removed, subsequent block processing runs should find nothing left in those queue positions and complete without error.

## Why This Matters

Chains that have undergone upgrades removing certain message types can end up with proposals that are permanently undecodable. Without this fix, the end-of-block processor would either crash the node or leave a stale entry in the queue that prevents normal block processing. The fix ensures the chain can continue operating normally after such an upgrade, automatically cleaning up any unprocessable proposals it encounters.
