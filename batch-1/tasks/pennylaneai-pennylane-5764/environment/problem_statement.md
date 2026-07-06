## Description

The quantum chemistry module lacks a unified, high-level function for computing the molecular dipole moment operator. Currently, users must manually select and combine lower-level functions depending on which computational backend they want to use (the built-in differentiable solver or the external plugin). Additionally, one important qubit encoding scheme — the parity transformation — is entirely missing from the existing observable conversion utility, limiting the choice of fermion-to-qubit mappings available to users.

## Expected Behavior

- A new top-level function should be available in the quantum chemistry module that accepts a molecule description and returns the three-component dipole moment operator (x, y, z) as qubit observables.
- The function should support both available computational backends through a single consistent interface.
- All three standard fermion-to-qubit mappings should be supported: the Jordan-Wigner, parity, and Bravyi-Kitaev transformations.
- The function should accept optional active space parameters, custom wire labels, and differentiable coordinate arguments.
- The function should correctly handle both Bohr and Angstrom coordinate units, producing equivalent operators for equivalent geometries.
- The returned dipole observable coefficients must be real-valued.
- Clear, informative error messages should be raised for unsupported backends, unsupported mappings, and open-shell molecules.
- The existing lower-level observable utility should be updated so that its error message reflects the addition of parity as a supported mapping.

## Why This Matters

Users computing molecular properties such as dipole moments currently face a fragmented API that requires knowing which backend-specific function to call, and they lack access to the parity transformation entirely. A unified high-level interface reduces friction, enables consistent behavior across backends, and expands the set of available qubit encodings.
