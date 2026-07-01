Implement the unitary-checking and matrix-extraction protocols for linear combinations of gates, operations, and sums of Pauli operators in Cirq. Enhance Pauli string objects to compute their matrix over a user-specified set of qubits, reflecting the string's coefficient and including identity factors for absent qubits.

*   Update `LinearCombinationOfGates` in `cirq/ops/linear_combinations.py`:
    *   Implement `_has_unitary_()` to return `True` if the combination's matrix is unitary, otherwise `False`.
    *   Implement `_unitary_()` to return the unitary matrix if unitary, or raise `TypeError` or `ValueError` otherwise.

*   Update `LinearCombinationOfOperations` in `cirq/ops/linear_combinations.py`:
    *   Implement `_has_unitary_()` to return `True` if the combination's matrix is unitary, otherwise `False`.
    *   Implement `_unitary_()` to return the unitary matrix if unitary, or raise `TypeError` or `ValueError` otherwise.

*   Update `PauliSum` in `cirq/ops/linear_combinations.py`:
    *   Implement `_has_unitary_()` to return `True` if the sum's matrix is unitary, otherwise `False`.
    *   Implement `_unitary_()` to return the unitary matrix if unitary, or raise `ValueError` otherwise.

*   Enhance `PauliString` in `cirq/ops/pauli_string.py`:
    *   Implement `matrix(qubits: Optional[Iterable[cirq.Qid]] = None) -> np.ndarray`.
    *   Ensure `matrix()` uses `self.qubits` if `qubits` is `None`.
    *   Include the string's coefficient in the returned matrix.
    *   For specified `qubits`, ensure absent qubits contribute an identity factor.
    *   Ensure the matrix dimension is `2^len(qubits)`.
    *   Reflect the qubit ordering in the tensor product ordering of the result.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.