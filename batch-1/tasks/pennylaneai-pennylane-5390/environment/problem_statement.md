## Description

PennyLane currently supports one method for transforming fermionic operators into qubit operators suitable for quantum hardware. We should add a second transformation method — the Bravyi-Kitaev transform — to give users more options when preparing quantum chemistry simulations.

## Expected Behavior

- Users should be able to apply the Bravyi-Kitaev transformation to any fermionic operator (a single fermionic term or a sum of fermionic terms) by specifying the total number of qubits in the system.
- The function should return either a general qubit operator or a Pauli sentence representation, depending on a flag the user provides.
- The function should support an optional mapping from fermionic mode indices to arbitrary qubit wire labels, enabling flexible targeting of physical qubits.
- The function should accept an optional numerical tolerance parameter for suppressing negligible floating-point noise in the output coefficients.
- Appropriate errors should be raised when the input is an incompatible type or when the fermionic mode indices are inconsistent with the specified number of qubits.
- Edge cases such as the identity operator and operators that simplify to zero should be handled correctly and return the appropriate null or identity qubit operator representation.

## Why This Matters

Quantum chemistry practitioners often want to compare different fermion-to-qubit mappings, as different encodings have different tradeoffs in terms of qubit count, locality, and circuit depth. Adding the Bravyi-Kitaev transform alongside the existing option makes PennyLane more useful for this kind of exploration.
