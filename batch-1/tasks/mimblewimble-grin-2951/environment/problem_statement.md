## Description

When a new node needs to sync the full chain state from peers, it currently requests a snapshot at an arbitrary block height. Because each peer may choose a different block to serve, it's impossible for a syncing node to download the same state from multiple peers in parallel — they may each offer a different snapshot, so the pieces are incompatible.

We need peers to agree on a single, consistent archive point that all nodes compute the same way. The archive point should be a block height that:
- Falls well behind the current chain tip (at least as far back as the state sync threshold)
- Lands on a regular interval boundary so all nodes independently compute the same height

This way, a syncing node can request the same state from multiple peers in parallel and reassemble the pieces reliably.

## Expected Behavior

- There should be a method on the chain that returns the block header representing the "archive point" — the snapshot peers are currently offering for state sync.
- The archive height should be calculated deterministically: take the current head height, subtract the sync threshold, then round down to the nearest multiple of a defined archive interval.
- In automated testing mode, the archive interval should be a small number (10 blocks) to make testing feasible.
- In production, the archive interval should correspond to approximately 12 hours worth of blocks.
- The existing testing configuration for cut-through horizon should be split into separate values for automated testing and user testing modes, rather than sharing one value.

## Why This Matters

State sync is a critical part of onboarding new nodes. Enabling parallel downloads from multiple peers would significantly reduce sync time and improve reliability. Without a consistent archive point, parallel syncing is not possible.
