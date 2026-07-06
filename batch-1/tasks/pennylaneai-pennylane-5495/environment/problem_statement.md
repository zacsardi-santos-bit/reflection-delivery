## Description

The qutrit mixed-state simulation device is currently only a skeleton. When you try to run a circuit on it, it returns nothing instead of any actual result. This makes the device completely unusable for simulating noisy qutrit (three-level quantum system) circuits.

We need a full implementation that can:

- Actually execute qutrit circuits and return correct measurement results (expectation values, samples, counts, probabilities)
- Support shot-based measurements and shot vectors
- Support automatic differentiation via backpropagation across all major machine learning frameworks
- Correctly integrate with PennyLane's execution tracker so users can monitor how many circuit runs, shots, and resources are consumed
- Expose a function that determines which observables the device accepts, including composite ones (products, sums, and tensor products of elementary observables) — not just the primitive observable types

## Expected Behavior

- Executing a circuit on the device returns a valid result instead of nothing
- The device supports the standard device API: read-only shots and wires properties, a correct device name, and backpropagation support detection
- A dedicated observable validity checker function is publicly importable and correctly handles composite observable types (not just individual observable names)
- The tracker integration records batches, executions, simulations, results, resources, errors, and shot counts
- Devices initialized with the same random seed produce identical results; a global random seed does not override the device-level seed; stateless random keys work correctly as seeds

## Why This Matters

Without a working qutrit mixed-state simulation device, users cannot simulate three-level quantum systems with noise. This is a foundational feature needed before any higher-level qutrit functionality can be used.
