I'm working on the Sui TypeScript SDK and need the SDK to properly support real end-to-end operations against a local node. Right now there's only a placeholder for this functionality. The SDK needs to handle reading transaction data, reading owned objects, and executing transactions of various kinds.

One important feature to add is a "local" transaction builder — a serializer that builds transaction bytes entirely on the client without calling the server to generate them. This means the signer should be able to accept an optional local serializer and use it to build transactions for split-coin, merge-coin, Move function calls, object transfers, and SUI transfers. Each of those operations should execute successfully when submitted to a full node.

The same set of transaction operations should also work via the standard RPC-backed approach against a gateway node, to confirm that path works too.

Additionally, the SDK needs to support basic read operations: getting the total number of transactions, fetching a recent transaction by its digest and verifying the digest is preserved in the response, reading objects owned by an address, and checking the Move type of a coin object.

The SDK should also provide an optional provider variant that caches object state, so it can be used as a drop-in replacement for the standard provider.
