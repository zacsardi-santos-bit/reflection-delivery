## Description

The Sui TypeScript SDK currently has no real end-to-end operational coverage — only a placeholder with a "todo" comment. The SDK needs comprehensive support for its core read and write APIs against a running node, so that operations such as reading transaction data, reading owned objects, and executing transactions actually work correctly.

A related gap is that the SDK only supports building transactions via a server-side RPC call (where the full node constructs the transaction bytes). Developers who want to build and sign transactions entirely on the client side — without a network round-trip — currently have no supported path. A local serializer that constructs transaction bytes from within the SDK itself would fill this need.

Additionally, applications that read the same objects repeatedly benefit from a provider implementation that caches fetched object state, avoiding redundant network requests.

## Expected Behavior

- The SDK can read the total number of transactions and retrieve individual transaction details (including the transaction digest) from a running node.
- The SDK can read objects owned by an address, retrieve individual objects, and determine the Move type of an object.
- A signer can execute all major transaction types — split coin, merge coin, Move call, transfer object, transfer SUI — against a gateway node, with each completing successfully.
- A signer can execute those same transaction types using a locally-built transaction serializer against a full node, with each completing successfully.
- A provider variant with object caching can be constructed and used in place of the standard provider.

## Why This Matters

Without these tests, regressions in basic SDK functionality go undetected. The local transaction serializer also unlocks offline signing and lower-latency transaction building for applications that cannot or do not want to rely on a server to construct transaction bytes.
