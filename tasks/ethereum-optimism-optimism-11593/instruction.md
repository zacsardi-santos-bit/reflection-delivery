Implement two helper contracts, `DeployOPChainInput` and `DeployOPChainOutput`, in a new deployment script file to manage OP Chain deployment inputs and outputs. Ensure these contracts validate inputs and outputs, provide clear error messages, and are importable alongside the main deployment script contract.

*   Implement `DeployOPChainInput` contract:
    *   Define a `Roles` struct with the following address fields:
        *   `opChainProxyAdminOwner`
        *   `systemConfigOwner`
        *   `batcher`
        *   `unsafeBlockSigner`
        *   `proposer`
        *   `challenger`
    *   Define an `Input` struct with:
        *   A `Roles` field named `roles`
        *   A `uint32` field named `basefeeScalar`
        *   A `uint32` field named `blobBaseFeeScalar`
        *   A `uint256` field named `l2ChainId`
    *   Expose a public boolean state variable `inputSet` initialized to `false`.
    *   Implement `loadInput(Input memory _input)` to store the input and set `inputSet` to `true`.
    *   Provide individual getter functions for each field in `Input` and a function `input()` returning the full `Input` struct.
    *   Ensure all getter functions (except `inputSet`) revert with "DeployOPChainInput: input not set" if called before `loadInput`.

*   Implement `DeployOPChainOutput` contract:
    *   Define an `Output` struct with the following fields:
        *   `ProxyAdmin opChainProxyAdmin`
        *   `AddressManager addressManager`
        *   `L1ERC721Bridge l1ERC721BridgeProxy`
        *   `SystemConfig systemConfigProxy`
        *   `OptimismMintableERC20Factory optimismMintableERC20FactoryProxy`
        *   `L1StandardBridge l1StandardBridgeProxy`
        *   `L1CrossDomainMessenger l1CrossDomainMessengerProxy`
        *   `OptimismPortal2 optimismPortalProxy`
        *   `DisputeGameFactory disputeGameFactoryProxy`
        *   `DisputeGameFactory disputeGameFactoryImpl`
        *   `AnchorStateRegistry anchorStateRegistryProxy`
        *   `AnchorStateRegistry anchorStateRegistryImpl`
        *   `FaultDisputeGame faultDisputeGame`
        *   `PermissionedDisputeGame permissionedDisputeGame`
        *   `DelayedWETH delayedWETHPermissionedGameProxy`
        *   `DelayedWETH delayedWETHPermissionlessGameProxy`
    *   Implement `set(bytes4 sel, address _addr)` to store an address for the specified output field.
    *   Provide typed getter functions for each field in `Output`.
    *   Ensure getters revert with "DeployUtils: zero address" if the stored address is zero.
    *   Ensure getters revert with "DeployUtils: no code at <addr>" if the stored address has no deployed bytecode.

*   Ensure both `DeployOPChainInput` and `DeployOPChainOutput`, along with `DeployOPChain`, are defined in `packages/contracts-bedrock/scripts/DeployOPChain.s.sol` and are importable.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.