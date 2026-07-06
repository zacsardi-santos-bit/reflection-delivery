Update the hardhat plugin package to be compatible with the latest version of the Ethereum JavaScript library. Implement changes to handle asynchronous address retrieval for contracts and introduce a new property to track the deployed Unlock contract address directly in the development environment.

*   Implement the `unlockAddress` property on the HardhatUnlockPlugin HRE extension:
    *   Initialize `unlockAddress` as `undefined` when the extension is created.
    *   Set `unlockAddress` to the deployed Unlock contract's address string after `deployProtocol()` completes using `await unlock.getAddress()`.

*   Update the `deployProtocol` function:
    *   Use ethers v6 API to deploy the Unlock protocol.
    *   Set `hre.unlock.unlockAddress` to the deployed contract's address via `await unlock.getAddress()`.

*   Modify the `deployLockTask` function:
    *   Accept `keyPrice` as a plain integer.
    *   Return the lock address using `await lock.getAddress()`.

*   Update the `createLock` function:
    *   Ensure `lockAddress` in the return object equals `await lock.getAddress()`.
    *   Accept `keyPrice` as `string | number | bigint`.

*   Ensure all contract objects returned by plugin methods (`deployProtocol`, `createLock`, `getLockContract`, `getUnlockContract`, `deployAndSetTemplate`) are ethers v6 compatible:
    *   Retrieve addresses via an async `getAddress()` method.

*   Update the `getUnlockContract` function:
    *   Retrieve the contract address using `hre.unlock.networks[chainId.toString()]`.
    *   Fall back to `hre.unlock.unlockAddress` if the address is not found in networks.

*   Ensure the `getLockContract` function returns an ethers v6 Contract instance.

*   Update fixture data:
    *   Use `ethers.ZeroAddress` for the zero address constant.
    *   Use `ethers.parseEther('.001')` for key price, returning a BigInt.

*   Update the networks configuration on the HRE unlock extension:
    *   Include entries for network IDs `1`, `12345`, and `31337`.
    *   Ensure `Object.keys(hre.unlock.networks).includes('12345')` is true.

*   Update the test fixture project configuration:
    *   Import `@nomicfoundation/hardhat-toolbox` to enable the ethers v6 toolbox.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.