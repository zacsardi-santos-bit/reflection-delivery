## Description

PennyLane is missing a built-in implementation of the qubitization algorithm. Qubitization is a foundational quantum computing technique that encodes a Hamiltonian into a unitary operator in a way that embeds the Hamiltonian's eigenvalues into the eigenvalues of that unitary. This is a key building block for efficient quantum phase estimation and many other quantum algorithms.

Currently, users who want to estimate Hamiltonian eigenvalues via quantum phase estimation must manually construct the block-encoding, handle phase corrections for negative coefficients, and compose the required sequence of amplitude-preparation, controlled-selection, and reflection steps. This is error-prone and not composable with PennyLane's differentiation framework out of the box.

## Expected Behavior

- A new quantum operator should be available that accepts a Hamiltonian (expressed as a linear combination of unitary operators) and a set of control qubits.
- The operator must automatically decompose into the standard qubitization circuit: amplitude preparation on the control register, controlled application of the Hamiltonian terms (with phase corrections for negative coefficients), the adjoint of the amplitude preparation, and a reflection step.
- Negative Hamiltonian coefficients must be handled by absorbing the sign into a global phase on each term, so all effective coefficients in the circuit are positive.
- The operator must be compatible with iterative quantum phase estimation routines, yielding accurate Hamiltonian eigenvalue estimates.
- The operator must be differentiable with respect to the Hamiltonian coefficients across multiple automatic differentiation frameworks.
- The operator must support both modern and legacy representations of Hamiltonians as linear combinations of Pauli operators.

## Why This Matters

Qubitization is used in many quantum simulation and quantum chemistry algorithms. Adding it as a first-class PennyLane operator allows researchers to build QPE-based algorithms compositionally, differentiate through them, and run them on various devices without low-level boilerplate.
