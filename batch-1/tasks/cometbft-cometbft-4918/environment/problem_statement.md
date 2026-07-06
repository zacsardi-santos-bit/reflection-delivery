## Description

A malicious peer in the block sync network can cause a node to get permanently stuck in block synchronization. The attack works as follows: the malicious peer first reports an absurdly large blockchain height (for example, the maximum integer value), causing the pool to record this as the target height to reach. The peer then reports a much lower, honest-looking height. Because the pool's maximum-height tracking only updates when the peer responsible for that maximum is removed with an exact height match, the pool never corrects its target — it remains stuck chasing an impossible height and never transitions out of block sync.

## Expected Behavior

- When a peer reports a height (or base) lower than it previously reported, the pool should treat this as malicious behavior.
- Such a peer should be removed from the active peer set and temporarily banned.
- After banning the malicious peer, the pool should recalculate the maximum known height based on the remaining honest peers.
- The pool should be able to proceed normally and eventually catch up with the real blockchain state after the malicious peer is removed.

## Why This Matters

Without this fix, a single malicious peer can permanently stall any node's block synchronization by reporting a fraudulently high height and then retracting it. This is a denial-of-service vector that requires no special privileges. Nodes would never exit block sync mode, rendering them unable to participate in consensus.
