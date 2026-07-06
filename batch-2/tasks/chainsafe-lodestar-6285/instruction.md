Update the beacon node's CLI package to resolve compilation errors due to outdated dependency imports. Implement the necessary changes to ensure that the codebase compiles and the beacon node startup behavior functions correctly.

*   Modify the `initPeerIdAndEnr` function in `packages/cli/src/cmds/beacon/initPeerIdAndEnr.ts`:
    *   Import `SignableENR` and `createPrivateKeyFromPeerId` from `@chainsafe/enr`.
    *   Use `createPrivateKeyFromPeerId(peerId).privateKey` as the key argument in calls to `SignableENR.createV4()` and `SignableENR.decodeTxt()`.
    *   Import `PeerId` type from `@libp2p/interface` instead of `@libp2p/interface/peer-id`.
    *   Ensure it creates a fresh peer ID and ENR when `persistNetworkIdentity` is false.
    *   Reuse existing peer ID and ENR when `persistNetworkIdentity` is true and both are valid and matching.
    *   Overwrite invalid or unreadable peer ID or ENR files when `persistNetworkIdentity` is true.
    *   Create and save a new ENR if the saved ENR does not match the saved peer ID.

*   Update all CLI source files to import `ENR` from `@chainsafe/enr`:
    *   `packages/cli/src/cmds/bootnode/handler.ts`
    *   `packages/cli/src/networks/index.ts`
    *   `packages/cli/src/options/beaconNodeOptions/network.ts`
    *   `packages/cli/src/config/peerId.ts`

*   Ensure the `@chainsafe/enr` package is listed as a dependency in `package.json` files:
    *   `packages/beacon-node/package.json`
    *   `packages/cli/package.json`

*   Implement the `isLocalMultiAddr` function in the appropriate handler or util file in `packages/cli/src/cmds/beacon/`:
    *   Return `true` for multiaddr addresses using `127.0.0.1`.
    *   Return `false` for addresses using `0.0.0.0`.

*   Update the beacon args handler to:
    *   Merge bootnodes from both a file and CLI arguments into a single list.
    *   Overwrite ENR fields when ENR-related CLI arguments are provided.

*   Ensure any class implementing the `Stream` interface from `@libp2p/interface` includes a `log` property initialized with a logger instance.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.