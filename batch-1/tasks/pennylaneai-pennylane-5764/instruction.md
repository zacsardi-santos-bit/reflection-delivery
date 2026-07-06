Implement a new high-level function `molecular_dipole` in the quantum chemistry module to compute the molecular dipole moment operator for a given molecule. Ensure it supports multiple computational backends and fermion-to-qubit mappings, including the parity transformation. Update the existing observable utility to reflect the addition of parity as a supported mapping.

*   Implement the `molecular_dipole` function in `pennylane/qchem/openfermion_pyscf.py` with the following signature:
    *   `molecular_dipole(molecule, method="dhf", active_electrons=None, active_orbitals=None, mapping="jordan_wigner", outpath=".", wires=None, args=None, cutoff=1.0e-16)`
    *   Return a list of three qubit observables for the x, y, and z components of the dipole moment.
*   Ensure the function supports:
    *   `method` values: 'dhf' (default) and 'openfermion'.
    *   `mapping` values: 'jordan_wigner' (default), 'parity', and 'bravyi_kitaev'.
    *   Real-valued coefficients for all returned dipole observables.
    *   Correct handling of Bohr and Angstrom coordinate units to produce equivalent operators.
*   Raise informative `ValueError` messages for:
    *   Unsupported methods, e.g., "Only 'dhf', and 'openfermion' backends are supported".
    *   Open-shell molecules, e.g., "Open-shell systems are not supported".
    *   Unsupported mappings, e.g., "'bksf' is not supported."
*   When a custom `wires` list is provided, ensure the wire labels of each returned dipole observable are a subset of the provided wiremap.
*   Export the `molecular_dipole` function from `pennylane/qchem/__init__.py` as `qml.qchem.molecular_dipole`.
*   Update the `observable` function in `pennylane/qchem/openfermion_pyscf.py` to include 'parity' as a valid mapping and modify its error message to: "Please set 'mapping' to 'jordan_wigner', 'parity', or 'bravyi_kitaev'".

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.