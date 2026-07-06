## Description

The plotnft command suite (create, show, join, leave, claim, inspect, change payout instructions, get login link) currently uses a function-based implementation that is difficult to test in an automated fashion. The existing tests for these commands have been entirely skipped because the implementation cannot be exercised programmatically — only through the full CLI runner. In addition, the error handling is incomplete: when errors occur, commands silently print a message and return instead of raising proper exceptions that callers can detect.

## Expected Behavior

- Each plotnft command should be implemented as an instantiable class with an async run method so that tests can create instances with known parameters and invoke them directly.
- Commands should raise proper exceptions (not just print and return) when errors occur, making error conditions detectable and testable.
- The full end-to-end workflow should be testable: creating pool NFTs in different states (self-pooling or joining a pool), joining and leaving pools, claiming rewards, inspecting wallet state, showing wallet status, changing payout instructions for a launcher ID, and retrieving pool login links.
- When no pool wallet is found and none is specified, commands should report the error clearly.
- When the specified wallet ID does not correspond to a pool wallet, commands should report that specifically.
- When multiple pool wallets exist and no ID is specified, commands that require a unique wallet should raise an appropriate error.
- Pool URL validation (HTTPS required on mainnet) and pool protocol validation (lock height limits, protocol version checks) should raise exceptions with descriptive messages.

## Why This Matters

Having testable, class-based command implementations ensures that pool NFT operations work correctly end-to-end across different wallet configurations (trusted/untrusted nodes, reuse/new puzzle hash derivations). The previous skip markers meant there was no automated validation of the pool NFT CLI workflow, leaving potential regressions undetected.
