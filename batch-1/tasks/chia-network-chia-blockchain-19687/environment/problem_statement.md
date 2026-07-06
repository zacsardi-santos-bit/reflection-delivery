## Description

Two improvements are needed in the blockchain node codebase.

**Fee store module placement**: The class responsible for storing fee estimation data is currently located in the full-node module, even though it is conceptually a shared protocol-layer component. Any code that needs fee store functionality is forced to depend on the full-node module even when it has nothing else to do with full-node internals. The class should be moved to the protocols module so it can be imported from the appropriate, logically correct location.

**Proper error signaling for non-canonical coin solutions**: When a spend bundle is submitted to the mempool and the dedup-eligible coin spend within it has a solution that uses non-canonical encoding, the system should explicitly reject the spend with a specific "invalid coin solution" error. Currently, the mempool silently alters the spend's eligibility status without returning the appropriate error code to signal why the spend was rejected. This makes it impossible for callers to distinguish this failure mode from others.

## Expected Behavior

- The fee store should be importable from the protocols module path.
- Submitting a spend bundle where a dedup-eligible coin spend contains a non-canonically encoded solution should result in a failure with an explicit "invalid coin solution" error code reported back to the caller.

## Why This Matters

These changes improve module organization and make error handling more precise and informative for consumers of the mempool API.
