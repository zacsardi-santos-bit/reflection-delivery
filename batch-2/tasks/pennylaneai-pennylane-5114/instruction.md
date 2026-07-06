Implement a utility function to correct the sign convention of wavefunctions imported from external quantum chemistry solvers to ensure accurate energy expectation values in PennyLane. Integrate this correction into the relevant state conversion routines.

*   Implement the `_sign_chem_to_phys(fcimatr_dict, norb)` function:
    *   Accept a wavefunction dictionary mapping (int, int) Fock-occupation tuples to float coefficients and an integer number of spatial orbitals.
    *   Return a new dictionary with corrected signs, converting from chemistry spin ordering (all spin-up first) to physics spin-orbital ordering (interleaved).
    *   Compute the sign for each entry as (-1) raised to the number of beta electrons occupying orbitals with lower index than each alpha electron.

*   Update the `_dmrg_state` function:
    *   Ensure it does not apply any sign correction to the wavefunction coefficients.
    *   Map each DMRG determinant directly to its (alpha_int, beta_int) key and store the coefficient without parity multiplication.
    *   Remove any previous parity computation.

*   Update the `_shci_state` function:
    *   Apply `_sign_chem_to_phys` to its output before returning.
    *   Infer the number of spatial orbitals (norb) as the length of the first determinant string.

*   Update the `import_state` function:
    *   Accept DMRG-format wavefunction input as a tuple of (list of integer lists, numpy array of float coefficients).
    *   Convert it to a normalized state vector in the full Fock space and return a numpy array matching the true DMRG ground-state energy to within an absolute tolerance of 1e-6.
    *   Accept SHCI-format wavefunction input as a tuple of (list of strings encoding occupations, numpy array of float coefficients).
    *   Convert it to a normalized state vector in the full Fock space and return a numpy array matching the true SHCI ground-state energy to within an absolute tolerance of 1e-6.

*   Ensure `_sitevec_to_fock` function remains accessible as `qml.qchem.convert._sitevec_to_fock`:
    *   Return a (int_a, int_b) Fock state tuple for both DMRG integer-list format and SHCI string format inputs.
    *   Maintain its current functionality as previously tested.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.