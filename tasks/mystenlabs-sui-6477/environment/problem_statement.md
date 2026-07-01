## Description

The Sui blockchain's RPC API is missing an endpoint to retrieve the current on-chain system state. Clients — including wallets, explorers, and other tooling — have no way to query validator information, staking pools, protocol parameters, and epoch details through the RPC interface. Additionally, addresses returned throughout the system are missing the standard "0x" prefix, making them appear as raw hexadecimal strings instead of the conventional prefixed format that users and tooling expect.

## Expected Behavior

- A new Full Node API endpoint should be available that returns the complete system state. The response should include:
  - Current epoch number and reference gas price
  - A unique object identifier for the state object
  - System parameters (such as maximum validator count, minimum stake, and storage gas price)
  - Storage fund and treasury cap balances
  - Validator report records
  - The full validator set (active validators, pending validators, pending removals, and next-epoch validator metadata)
  - Each validator should expose its stake amount, pending changes, commission rate, gas price, and delegation staking pool information
  - Each staking pool should expose its balance, pending delegation entries, pending withdrawal entries, token supply, and starting epoch

- All address values serialized in API responses and configuration snapshots must appear as quoted strings with the "0x" prefix rather than bare hexadecimal strings. This includes validator addresses throughout the system state.

## Why This Matters

Without this endpoint, applications cannot inspect the current network configuration or staking state without direct object queries, making it difficult to build validator dashboards, staking UIs, or protocol monitoring tools. The address formatting inconsistency also causes interoperability issues with tooling that expects the "0x" prefix convention.
