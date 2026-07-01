Implement functions to facilitate conversion between PennyLane and an external quantum chemistry library's operator formats. Ensure support for both fermionic and qubit operators, with options for wire remapping and tolerance settings for handling complex coefficients.

*   Update `_to_string` in `pennylane/fermi/fermionic.py`:
    *   Accept a `FermiWord` and an optional `of` parameter (default `False`).
    *   Return a string in PennyLane format when `of=False` and OpenFermion format when `of=True`.
    *   Return `"I"` for an empty `FermiWord` in both formats.
    *   Raise `ValueError` with `"fermi_op must be a FermiWord, got: {type(fermi_op)}"` if input is not a `FermiWord`.

*   Implement `_import_of` in `pennylane/qchem/convert_openfermion.py`:
    *   Import and return the openfermion module.
    *   Raise `ImportError` with `"This feature requires openfermion"` if the module is unavailable.

*   Implement `_from_openfermion_qubit` in `pennylane/qchem/convert_openfermion.py`:
    *   Convert an OpenFermion `QubitOperator` to a PennyLane `LinearCombination` by default.
    *   Accept a `tol` parameter (default `1.0e-16`) to control imaginary coefficient retention.
    *   Accept a `format` keyword argument:
        *   `format="LinearCombination"` returns `qml.ops.LinearCombination`.
        *   `format="Sum"` returns `qml.ops.Sum`.
        *   Raise `ValueError` with `"format must be a Sum or LinearCombination, got: <format_value>"` for other values.

*   Add `to_openfermion` to `pennylane/qchem/convert_openfermion.py`:
    *   Export from `pennylane/qchem/__init__.py` and make accessible as `qml.to_openfermion`.
    *   Convert a PennyLane operator (`FermiWord`, `FermiSentence`, `Sum`, or `LinearCombination`) to an OpenFermion operator.
    *   Accept a `wires` parameter (default `None`) for wire remapping.
        *   Raise `ValueError` with `"Supplied \`wires\` does not cover all wires defined in \`ops\`."` if incomplete.
    *   Accept a `tol` parameter (default `1.0e-16`) for imaginary coefficient handling.
    *   Raise `ValueError` with `"pl_op must be a Sum, LinearCombination, FermiWord or FermiSentence, got: {type(pl_op)}."` for unsupported types.
    *   Raise `ValueError` matching `"Expected a Pennylane operator with a valid Pauli word representation,"` for invalid Pauli words.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.