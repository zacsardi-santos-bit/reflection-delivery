I'm working with a quantum simulator that represents quantum states using the matrix product state (MPS) format. The simulator has support for creating MPS objects, setting their raw tensor data, and converting them to full wavefunctions — but it's missing a direct way to compute the inner product between two MPS states.

I need to add a method to the MPS statespace class that computes the complex-valued overlap between two MPS quantum states of equal size (same number of qubits and bond dimension). The operation should contract the tensor network directly — working through the qubit sites from left to right — and return a complex scalar. This avoids having to expand both states into full wavefunction vectors, which would be exponentially expensive.

The method should work correctly for entangled multi-qubit states with non-trivial bond dimensions, returning both the real and imaginary parts of the overlap accurately.
