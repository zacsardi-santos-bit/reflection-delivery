## Description

When working with state-vector quantum simulators that are capable of handling non-commuting measurements in a single circuit execution, users often need to measure expectation values of Hamiltonians or multi-term observables. However, many such simulators don't natively support multi-term observable types — they can only process individual single-term measurements directly.

The existing transform for splitting non-commuting observables solves a different problem: it distributes non-commuting terms across multiple separate circuit executions. For simulators that can already handle all terms in one execution, this approach is unnecessary and inefficient.

## Expected Behavior

A new transform should be added that:

- Takes a circuit with expectation value measurements of multi-term observables and converts them into individual single-term measurements, all within a **single** circuit execution
- Automatically deduplicates identical observable terms that appear across multiple measurements, measuring each unique term only once and reusing the result
- Correctly handles constant identity terms as numeric offsets, without requiring any additional device measurements
- Works with batches of circuits, shot vectors, and batched circuit parameters
- Is differentiable with all major automatic differentiation frameworks
- When the circuit already contains only single-term or wire-based measurements, it leaves the circuit unchanged with minimal overhead

Additionally, both this new transform and the existing non-commuting observable splitting transform should raise a clear, descriptive error when a user attempts to use a multi-term observable in a measurement type that fundamentally does not support observable decomposition (such as counting bitstring outcomes).

## Why This Matters

State-vector simulators that support non-commuting measurements in a single pass are common, and users should be able to measure Hamiltonian expectation values on them without either unnecessary overhead (multiple circuit executions) or manual decomposition. A dedicated transform for this use case makes this workflow straightforward and efficient.
