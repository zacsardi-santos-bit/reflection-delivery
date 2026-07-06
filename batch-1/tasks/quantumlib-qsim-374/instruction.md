Implement a method in the MPSStateSpace class to compute the complex-valued inner product between two matrix product state (MPS) quantum states of equal size. This method should directly contract the tensor network without expanding the states into full wavefunction vectors.

*   Update the `MPSStateSpace` class in `lib/mps_statespace.h`:
    *   Add a static method `InnerProduct` with the signature:
        *   `static std::complex<fp_type> InnerProduct(MPS& state1, MPS& state2)`
    *   Ensure `InnerProduct`:
        *   Accepts two MPS objects, `state1` and `state2`, with the same number of qubits and bond dimension.
        *   Returns a `std::complex<fp_type>` value representing the inner product `<state2|state1>`.
        *   Performs a full tensor network contraction across all qubit sites from left to right.
        *   Utilizes scratch space within `state1`'s raw memory beyond the `Size()` boundary for intermediate results.
        *   Accurately computes the real and imaginary parts of the overlap for typical entangled multi-qubit states.
        *   For a 4-qubit MPS state with bond dimension 4, returns a result with a real part approximately 0.5524 and an imaginary part approximately 0.2471, accurate to within 1e-4.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.