## Description

The quantum circuit expectation layer currently supports only noiseless analytic simulation. Users working with realistic noisy quantum circuits have no way to compute expectation values using trajectory-based noisy simulation through this layer. Adding support for a noisy backend would allow users to evaluate circuits that include depolarizing noise and other realistic noise channels.

Additionally, the backend currently only accepts a null value or a preconfigured simulator object, making it awkward to switch between modes. Supporting string-based backend identifiers for selecting between noisy and noiseless simulation modes would provide a cleaner, more user-friendly API.

## Expected Behavior

- The layer should accept string-based backend identifiers to select between noisy and noiseless simulation modes.
- When using the noisy backend, users must provide a repetitions count specifying how many stochastic trajectories to average over.
- If repetitions are missing when required, or provided when they shouldn't be, the layer should raise a descriptive error message explaining the issue.
- The noisy backend should support all the same input combinations (batched circuits, batched operators, symbol values) that the noiseless backend supports.
- The noisy backend should also support scalar, 1D, and 2D repetitions values to allow different numbers of trajectories per operator.

## Why This Matters

Users building hybrid quantum-classical models on noisy hardware simulators need a clean way to incorporate noise-aware expectation value computation into their Keras model graphs. Without this, they must work around the layer entirely or use lower-level APIs.
