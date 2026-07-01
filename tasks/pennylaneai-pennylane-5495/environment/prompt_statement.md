I'm trying to use PennyLane's qutrit mixed-state simulator device, but it doesn't actually do anything when I try to run a circuit on it — it just returns nothing. I need the device to be fully implemented so I can simulate noisy three-level quantum system circuits.

Specifically, I need the device to correctly execute quantum circuits and return expectation values, samples, counts, and probability distributions. It should support shot-based measurements and shot vectors, and it needs to work with automatic differentiation through backpropagation so I can compute gradients using any of the major ML frameworks.

The device should also integrate with PennyLane's execution tracker so that when I wrap execution in a tracker context, I can see how many circuit runs, simulations, shots, and resource counts were used.

Additionally, there's a function that determines whether a given observable is accepted by this device. It currently only handles primitive observable types but doesn't correctly handle composite observables like products, sums, tensor products, and scalar-multiplied observables. This function should recursively check composite types so that any valid combination of supported primitive observables is also accepted.

The device should behave correctly with random seeds: two devices initialized with the same seed should produce identical results for shot-based measurements, while a global random seed should not interfere with a device-level seed. The device should also accept stateless framework-style random keys as seeds with analogous reproducibility guarantees.
