Implement a new submodule in PennyLane to define lattice geometries and construct spin Hamiltonians. Create a `Lattice` class for defining lattice structures and a `transverse_ising` function to build Hamiltonians on these lattices.

Requirements:

*   Implement the `Lattice` class in `pennylane/spin/lattice.py` with:
    *   Constructor parameters: `n_cells` (list of positive ints), `vectors` (2D array), `positions` (optional 2D array), `boundary_condition` (bool or list of bools), `neighbour_order` (int).
    *   Raise `ValueError` for:
        *   `boundary_condition` as a list: "Argument 'boundary_condition' must be a bool".
        *   `vectors` as 1D array: "The dimensions of vectors array must be 2, got 1".
        *   `positions` as 1D array: "The dimensions of positions array must be 2, got 1".
        *   Non-square `vectors` array: "The number of primitive vectors must match their length".
    *   Raise `TypeError` for `n_cells` with non-positive or non-integer values: "Argument `n_cells` must be a list of positive integers".
    *   Attributes:
        *   `lattice_points`: Array of computed spatial coordinates.
        *   `edges`: List of 3-tuples representing connections.
        *   `vectors`, `positions`, `n_dim`, `boundary_condition`: Store respective data, with `boundary_condition` as a list of bools.
    *   `add_edge` method:
        *   Accept list of sublists, each of length 2 or 3.
        *   Raise `ValueError` for duplicate edges: "Edge is already present".
        *   Raise `TypeError` for wrong-length sublists: "Length of the tuple representing each edge can only be 2 or 3.".

*   Implement `_generate_lattice` function in `pennylane/spin/lattice.py`:
    *   Parameters: `lattice` (str), `n_cells` (list), `boundary_condition` (bool or list), `neighbour_order` (int).
    *   Return a `Lattice` object for standard geometries: chain, square, rectangle, honeycomb, triangle, kagome.
    *   Raise `ValueError` for unsupported names: "Lattice shape, '{name}' is not supported.".

*   Implement `transverse_ising` function in `pennylane/spin/spin_hamiltonian.py`:
    *   Parameters: `lattice` (str), `n_cells` (list), `coupling` (number, list, array-like, or None), `h` (float), `boundary_condition` (bool or list), `neighbour_order` (int).
    *   Return a PennyLane Hamiltonian with terms for each edge and site.
    *   Raise `ValueError` for incorrect `coupling` shape: "Coupling should be a number or an array of shape {neighbour_order}x1 or {N}x{N}".

*   Export `Lattice` and `transverse_ising` in `pennylane/spin/__init__.py` for import as `pennylane.spin`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.