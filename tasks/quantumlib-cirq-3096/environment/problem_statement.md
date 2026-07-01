## Description

Several important operator types in Cirq — linear combinations of gates, linear combinations of operations, and sums of Pauli operators — cannot currently be queried for unitarity or have their unitary matrix extracted through the standard library protocols. This means that even when such an operator is mathematically unitary, the library cannot recognize this, and attempts to retrieve its unitary representation fail or are unsupported.

Additionally, Pauli string objects can only compute their matrix on their own native qubits. There is no way to request the matrix over a user-specified collection of qubits (in a chosen order), including qubits that are not part of the string (which should contribute identity factors). This makes it cumbersome to embed or compare Pauli strings in the context of larger multi-qubit systems.

## Expected Behavior

- Linear combinations of gates should correctly report whether they are unitary and return the unitary matrix when asked, or reject the request with an appropriate error when they are not unitary.
- The same capability should apply to linear combinations of operations.
- Sums of Pauli operators should participate in the same unitary-checking and matrix-extraction protocol, returning the correct unitary or raising an error when not unitary.
- Pauli strings should support computing their matrix over a caller-specified set of qubits in a specified order. Qubits not belonging to the string should act as the identity. The result should incorporate the string's coefficient.

## Why This Matters

These improvements allow quantum algorithm code to uniformly use the standard unitary-checking and matrix-extraction interface on all of these operator types, and give users more flexibility when working with Pauli string operators in multi-qubit contexts.
