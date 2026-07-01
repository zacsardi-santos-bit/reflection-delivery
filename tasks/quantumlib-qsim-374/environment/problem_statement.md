## Description

The matrix product state (MPS) statespace component of the quantum simulator is missing the ability to compute the inner product between two quantum states. The inner product (also called overlap) is a fundamental operation that returns a complex scalar representing how similar two quantum states are, including their relative phase.

Currently, users can create MPS representations of quantum states and convert them to full wavefunctions, but there is no way to directly compute ⟨ψ₁|ψ₂⟩ between two MPS objects without expanding them into full wavefunctions first — which is expensive and defeats the purpose of the MPS representation.

## Expected Behavior

- The statespace object should provide an inner product operation that takes two MPS states (with matching qubit count and bond dimension) and returns a complex number.
- The result must correctly reflect both the magnitude and phase of the overlap.
- The computation should work directly on the MPS tensor data, contracting the tensor network site by site from left to right.
- Results must be numerically accurate (within reasonable floating-point tolerance) for typical entangled multi-qubit states.

## Why This Matters

Without this capability, comparing two quantum states in MPS form requires first converting both to full wavefunction vectors — an exponentially expensive operation in the number of qubits. A direct MPS-level inner product allows fidelity computations, expectation values, and state comparisons to remain efficient even for larger systems.
