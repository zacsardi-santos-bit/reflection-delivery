# Hard Fork Activation Should Be Network-Aware

## Description

The protocol version validation logic currently uses a single, fixed hard fork activation schedule regardless of which network a node is running on. This means the test network cannot have its own hard fork schedule — it's forced to use the same block heights as the main production network. This makes it impossible to test upcoming hard fork upgrades on the test network before they activate on mainnet.

Additionally, the first hard fork activation height on mainnet appears to be incorrectly gated — the second protocol version is not being accepted at the expected activation height, which breaks block validation at that boundary.

## Expected Behavior

- The header version validation logic should check which network is active (production or test network) and apply the appropriate hard fork schedule for that network.
- On the production network, the second protocol version should become valid starting at the first hard fork block height, and invalid before or after its active window.
- On the test network, the first hard fork should activate at an earlier, dedicated block height specific to that network, independent of the production schedule.
- A publicly accessible constant representing the test network's first hard fork block height should be defined and available for use throughout the codebase.
- Protocol version 3 should not yet be valid on either network at the current planned heights (no third hard fork has been scheduled yet).

## Why This Matters

Without per-network hard fork schedules, the test network cannot advance through protocol upgrades independently. This is critical infrastructure for safely rolling out consensus changes — operators and developers need to be able to test hard forks on the test network before they go live on mainnet.
