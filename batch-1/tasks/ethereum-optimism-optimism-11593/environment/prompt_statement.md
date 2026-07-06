I'm working on the OP Chain deployment infrastructure and need to introduce two helper contracts in a new deployment script file: one that manages deployment inputs and one that manages deployment outputs.

The input contract should hold all the configuration needed to deploy a new chain — specifically the operator role addresses (proxy admin owner, system config owner, batcher, unsafe block signer, proposer, and challenger), as well as fee scalars and the L2 chain ID. It should expose individual getters for each field and a getter for the full input structure, but these should revert with a clear error if the input hasn't been loaded yet.

The output contract should track all deployed contract addresses that result from the chain deployment — covering proxy admin, address manager, various bridge and messaging proxies, dispute game contracts, and delayed WETH proxies. It needs a way to set each address individually by specifying which output field to populate, and typed getter functions for each address that revert if the address is zero or if the address doesn't have any deployed code at it.

Both contracts, plus the main deployment script contract, should live in the same file and be importable from it. The code validation checks on output addresses should produce error messages that include the specific invalid address when the issue is missing code.
