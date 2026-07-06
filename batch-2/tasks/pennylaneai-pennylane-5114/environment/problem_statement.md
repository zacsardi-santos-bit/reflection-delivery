## Description

When importing wavefunctions from external quantum chemistry solvers — specifically those based on selected CI or density matrix renormalization group methods — the resulting PennyLane state vectors produce incorrect energy expectation values. The root cause is a sign convention mismatch: these external solvers store spin-up and spin-down electron occupations in a different ordering than PennyLane's internal physics convention. When the state is imported, the coefficient signs are not adjusted for this ordering difference, leading to phase errors in the resulting quantum state.

## Expected Behavior

- A utility function should be added that converts a wavefunction (represented as a dictionary of Fock-basis coefficients) from the chemistry spin-ordering convention to the physics spin-orbital ordering convention by flipping the appropriate coefficient signs.
- The state conversion functions for these solver types should apply this sign correction, so that the returned wavefunction dictionaries carry the correct phases.
- When a wavefunction is imported from either of these solver formats and used to compute an energy expectation value in PennyLane, the result should match the energy reported by the external solver to high numerical precision.

## Why This Matters

Incorrect phase factors in an imported wavefunction can cause PennyLane to compute wrong energies and wrong quantum circuit behaviors when initializing states from external solvers. Users who import states from these solvers to further process them in PennyLane (e.g., for variational optimization, property evaluation, or state preparation) will get silently wrong results. Fixing the sign convention ensures that PennyLane's quantum state accurately represents the chemistry solver's output.
