Implement support for multi-qubit gates in the QASM parser of Cirq to recognize and correctly parse common two-qubit and three-qubit gates from the standard QASM library. Ensure these gates can be applied to full qubit registers and handle errors appropriately when incorrect arguments or parameters are provided. Additionally, create a shared utility function for verifying QASM output consistency with an external simulator.

*   Extend `QasmParser` in `cirq/contrib/qasm_import/_parser.py` to support:
    *   Two-qubit gates: 'cx' and 'CX' (map to `cirq.CNOT`), 'cz' (map to `cirq.CZ`), 'cy' (map to `cirq.ControlledGate(cirq.Y)`), 'swap' (map to `cirq.SWAP`), 'ch' (map to `cirq.ControlledGate(cirq.H)`).
    *   Three-qubit gates: 'ccx' (map to `cirq.TOFFOLI` / `cirq.CCX`), 'cswap' (map to `cirq.CSWAP`).
    *   Ensure gates take 0 parameters and the correct number of qubit arguments (2 for two-qubit gates, 3 for three-qubit gates).
    *   Support register broadcasting for gates applied to full registers.
*   Implement error handling in `QasmParser.parse()`:
    *   Raise `QasmException` with appropriate messages if:
        *   A two-qubit gate receives only 1 qubit argument: ".*<gate_name>.* takes 2 arg(s).*got.*1.*line <N>".
        *   A two-qubit gate receives parameters: ".*<gate_name>.* takes 0 parameter(s).*got.*1.*line <N>".
        *   A three-qubit gate receives only 1 qubit argument: ".*<gate_name>.* takes 3 arg(s).*got.*1.*line <N>".
        *   A three-qubit gate receives parameters: ".*<gate_name>.*parameter.*line <N>.*".
*   Add `assert_qiskit_parsed_qasm_consistent_with_unitary` in `cirq/testing/consistent_qasm.py`:
    *   Importable as `from cirq.testing import consistent_qasm`.
    *   Callable as `consistent_qasm.assert_qiskit_parsed_qasm_consistent_with_unitary(qasm, unitary)`.
    *   Return `None` if qiskit is not installed.
    *   If qiskit is available, execute QASM via qiskit's unitary simulator, reverse qubit ordering, and assert closeness to the provided unitary with `rtol=1e-8` and `atol=1e-8`.
*   Ensure circuits containing TOFFOLI, CSWAP, SWAP, CX, ControlledGate(Y), CZ, ControlledGate(H), and common single-qubit gates are round-trippable through QASM, maintaining unitary consistency up to global phase (`atol=1e-8`).

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.