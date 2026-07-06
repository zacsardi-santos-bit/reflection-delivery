I'm working with a quantum simulator that can handle non-commuting measurements all in a single execution, but it doesn't natively support measuring expectation values of multi-term Hamiltonians or sums of observables directly. I need a circuit transform that automatically expands these multi-term observable measurements into individual single-term measurements — all kept on the same tape, not split into multiple circuit executions — and then combines the results correctly via a postprocessing step.

Ideally the transform should also be smart about deduplication: if the same observable appears in multiple different expectation value measurements, it should only be measured once, and the shared result should be reused across all the originals. Identity terms should be handled as plain numeric offsets without wasting a device measurement on them.

Additionally, I'd like both this new transform and the existing non-commuting splitting transform to raise a clear error message when someone tries to apply an observable sum to a measurement type that doesn't support decomposition (like counting bitstring outcomes), rather than producing confusing failures or silently giving wrong results.

The transform should be differentiable with major automatic differentiation frameworks, work with shot vectors and batched parameters, and be accessible as part of the standard transforms API.
