## Description

PennyLane needs a new submodule for constructing and working with spin Hamiltonians on lattice structures. Currently there is no built-in way to define a lattice geometry or automatically generate quantum spin models from it. Researchers working on condensed-matter quantum simulation need to be able to specify a lattice, get the lattice sites and their connections, and then build common Hamiltonians on top of it.

## Expected Behavior

- Users should be able to define a lattice by providing its primitive vectors, basis site positions, number of unit cells per dimension, and boundary conditions (open or periodic per dimension).
- The lattice object should expose the computed site coordinates and the list of neighbor connections (edges).
- A set of standard named lattice geometries (chain, square, rectangle, honeycomb, triangle, kagome) should be available and constructible from a name string. Name lookup should be case-insensitive and tolerant of surrounding whitespace.
- Users should be able to manually add edges to a lattice, with proper validation that prevents duplicate edges and enforces correct edge format.
- A transverse-field Ising Hamiltonian should be constructible from any lattice, with flexible coupling strengths (uniform value, 1-element array, or full coupling matrix) and a transverse field parameter.
- Clear, descriptive errors should be raised for invalid inputs: wrong array dimensions, non-positive cell counts, unsupported shape names, or malformed coupling arrays.

## Why This Matters

Without this capability, users have to manually construct lattice graphs and Hamiltonian terms from scratch every time they want to simulate a spin model, which is error-prone and time-consuming. Having a built-in lattice module with standard geometries and Hamiltonian builders significantly reduces the boilerplate needed for common quantum simulation tasks.
