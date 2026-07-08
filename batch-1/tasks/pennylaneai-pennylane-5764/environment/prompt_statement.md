I'm in the quantum chemistry module and I keep hitting friction computing molecular dipole moments. Right now there's no single high-level entry point, I have to know which backend-specific helper to call and stitch the workflow together myself depending on whether I'm using the built-in differentiable solver or the external plugin backend. I want one function that handles both through the same interface.

So I'd like a new top-level function added to the quantum chemistry module that takes a molecule object plus optional params for picking the backend, the fermion-to-qubit mapping, an active space, custom wire labels, and differentiable geometric (coordinate) arguments. It should return a list of three qubit observables for the x, y, and z components of the dipole moment operator. Coefficients on those returned operators need to always be real-valued, and it's gotta correctly handle both Bohr and Angstrom coordinate units so equivalent geometries give equivalent operators.

It should also raise clear, informative errors for unsupported backends, for unsupported mappings, and for open-shell molecules.

Oh and the parity transformation is missing entirely as a mapping option. Currently only two mappings exist in the relevant observable conversion utility (Jordan-Wigner and Bravyi-Kitaev), so I need parity added as a third supported fermion-to-qubit mapping, both wired into the function and reflected in that lower-level utility's error message when an unsupported mapping gets passed, since right now it doesn't mention parity at all. All three (Jordan-Wigner, parity, Bravyi-Kitaev) should work.

This cuts the friction of a fragmented API and expands the qubit encodings I can actually use.
