Implement a mechanism to detect and handle malicious peers in the block sync network that report decreasing blockchain heights or bases. Ensure that such peers are removed and banned, and that the system recalculates the maximum peer height based on remaining honest peers.

*   Modify the `SetPeerRange` method in `internal/blocksync/pool.go`:
    *   If a peer reports a height or base lower than previously reported, immediately remove and ban the peer.
    *   Ensure the method returns early without updating the peer's height, base, or the pool's maximum peer height.
*   Implement the `IsPeerBanned` method in `internal/blocksync/pool.go`:
    *   Return `true` if the specified peer is currently banned due to reporting a lower height or base.
*   Recalculate the maximum peer height from remaining peers after banning a malicious peer, allowing `IsCaughtUp()` to function correctly.
*   Define the `MaliciousTestMaximumLength` constant in `internal/blocksync/pool_test.go`:
    *   Set it to `5 * time.Minute` for use in malicious peer test scenarios.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.