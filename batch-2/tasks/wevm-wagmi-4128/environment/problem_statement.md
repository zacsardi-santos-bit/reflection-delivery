## Description

The library is missing support for the standard wallet "watch asset" capability, which lets a decentralized application request that a user's wallet starts tracking a specific token. This is a common flow — for example, after a token swap or airdrop, apps want to prompt users to add the new token to their wallet so it shows up in the balance view. Without this, developers have no built-in, type-safe way to trigger this prompt.

## Expected Behavior

- A core action should be available that allows requesting the connected wallet to watch/track a token. The action should accept the token type and asset details (contract address, symbol, and decimal precision) and return a boolean indicating whether the user accepted or rejected the request.
- A mutation options factory should be available in the query utilities module, with an appropriate mutation key identifying the watch-asset operation, so developers can integrate this action with their query/mutation management layer.
- A React hook should be available that wraps the core action as a mutation. It should expose a trigger method, a loading/success/error state, and the result data (a boolean).
- All three of these — the core action, the mutation options factory, and the React hook — must be exported from their respective package entry points so consumers can import them.
- The Vue package's action and query exports should also include the new core action and mutation options.

## Why This Matters

Developers building token-related flows (swaps, airdrops, bridges) need a reliable, idiomatic way to prompt wallet users to add a token to their watch list. Without this, they must either write low-level wallet calls manually or skip the feature entirely. Having it built into the library as a first-class action and hook brings it in line with other wallet interaction patterns already supported.
