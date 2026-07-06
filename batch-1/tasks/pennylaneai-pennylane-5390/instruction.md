Implement the `bravyi_kitaev` function in the `pennylane/fermi/conversion.py` file to enable the Bravyi-Kitaev transformation of fermionic operators into qubit operators. Ensure it handles both single fermionic terms and sums of terms, with options for output format, wire mapping, and tolerance for numerical precision.

*   Implement `bravyi_kitaev` with the following signature:
    *   `bravyi_kitaev(fermi_operator, n, ps=False, wire_map=None, tol=None)`
    *   Import from `pennylane.fermi.conversion`.

*   Input validation:
    *   Raise `ValueError` with message 'fermi_operator must be a FermiWord or FermiSentence' if `fermi_operator` is not of these types.
    *   Raise `ValueError` with message "Can't create or annihilate a particle on qubit index {index} for a system with only {n} qubits" if any fermionic mode index is >= `n`.

*   Output behavior:
    *   When `ps=False`, return a qubit operator composed of scaled Pauli products matching the Bravyi-Kitaev transform.
    *   When `ps=True`, return a `PauliSentence` matching the expected transform output.

*   Special cases:
    *   For an empty `FermiWord`, return `qml.Identity(0)` with `ps=False`, or `PauliSentence({PauliWord({0: 'I'}): 1.0 + 0.0j})` with `ps=True`.
    *   For zero operators, return a `PauliSentence` that simplifies to `None` with `ps=True`, or an `SProd` with scalar `0` and an `Identity` base with `ps=False`.
    *   For an empty `FermiSentence`, return `PauliSentence({})` after simplification with `ps=True`, or an `SProd` with scalar `0` and an `Identity` base with `ps=False`.

*   Wire mapping:
    *   Accept `wire_map` as a dict mapping fermionic mode indices to qubit wire labels.
    *   Use integer mode indices as qubit wire indices when `wire_map=None`.

*   Tolerance handling:
    *   Use `tol` to remove negligible imaginary components from coefficients.
    *   Return coefficients as floats when their imaginary part is <= `tol`, or as complex numbers when `tol=None`.
    *   Ensure the `.data` attribute of the returned operator reflects these coefficient values.

*   Ensure compatibility with both `FermiWord` and `FermiSentence` inputs for all parameter combinations.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.