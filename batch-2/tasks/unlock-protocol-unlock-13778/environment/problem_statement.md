## Description

The hardhat plugin package needs to be updated to work with a newer major version of the Ethereum JavaScript library. The upgrade introduces breaking API changes that make the existing plugin code incompatible. Most notably, deployed contract addresses can no longer be read from a synchronous property — they now require an asynchronous method call. The zero address constant and ether-parsing utilities have also moved to a different namespace in the new library version.

Beyond the library migration, the plugin's development environment extension needs a new top-level property to track the most recently deployed Unlock protocol contract address directly. Currently this address is only stored inside a network-specific configuration object, making it difficult to retrieve in development environments where the network may not be pre-registered. The new property should start as unset and be populated when the protocol is deployed.

## Expected Behavior

- A dedicated property for the deployed Unlock contract address should exist on the environment extension, initialized as unset, and populated after a successful protocol deployment
- All contract objects returned by plugin methods should expose their addresses via an asynchronous method call, compatible with the new library version
- The networks configuration registered on the environment extension should include all known network IDs from the shared networks package, including network ID 12345
- Fixture test data should use the updated library API for the zero address constant and for parsing ether values (which now returns a native large integer rather than the old wrapper object)
- The hardhat task for creating locks should return the contract address via the async address method
- The test fixture project configuration should import the updated toolbox package

## Why This Matters

The plugin currently fails tests because it uses outdated library APIs that no longer exist in the new library version. Developers using the plugin on a local hardhat network need to easily access the deployed Unlock contract address without pre-configuring network-specific settings.
