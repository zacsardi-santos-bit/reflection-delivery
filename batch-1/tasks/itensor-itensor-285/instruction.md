Implement QR decomposition functionality for the ITensor library to support tensor network computations. Ensure the decomposition works for real, complex, and quantum number structured tensors, providing both complete and thin forms. Include a low-level matrix routine for QR decomposition of real and complex matrices.

Requirements:

*   Implement the `qr` function in `itensor/decomp.h` and `itensor/decomp.cc` or `itensor/decomp_impl.h`.
    *   Signature: `qr(ITensor T, ITensor& Q, ITensor& R, Args const& args = Args::global()) -> void`
    *   Accept an ITensor `T`, output ITensor references `Q` and `R`.
    *   Use the optional `Args` object to specify "Complete" and "UpperTriangular" options.
    *   Ensure `T - Q*R` has a norm less than 1E-12.
    *   Tag the shared index between `Q` and `R` as 'Link,QR'.
    *   Support both complete and thin QR:
        *   "Complete" true: Link,QR index dimension equals the full row dimension.
        *   "Complete" false: Link,QR index dimension equals the smaller of row and column dimensions.
    *   Ensure `R` is upper triangular when "UpperTriangular" is true.
    *   Ensure `Q` is unitary: `Q†Q = Identity`. For complete QR, also ensure `QQ† = Identity`.
    *   Support ITensors with multiple indices assigned to `Q`.
    *   Support complex-valued ITensors.
    *   Support ITensors with quantum number structure, both zero and non-zero divergence.

*   Implement the `QR` function for real matrices in `itensor/tensor/algs.h` and `itensor/tensor/algs.cc` or `itensor/tensor/algs_impl.h`.
    *   Signature: `QR(Matrix const& M, Matrix& Q, Matrix& R, bool complete) -> void`
    *   Accept a real `Matrix` `M`, output `Matrix` references `Q` and `R`.
    *   Ensure `Q*R` reconstructs `M` within tolerance 1E-12*norm(M).
    *   Ensure `R` is upper triangular.
    *   Support both complete and thin QR:
        *   "Complete" true: `Q*transpose(Q) = Identity`.
        *   "Complete" false: `transpose(Q)*Q = Identity`.
    *   Handle tall and rank-deficient matrices.

*   Implement the `QR` function for complex matrices in `itensor/tensor/algs.h` and `itensor/tensor/algs.cc` or `itensor/tensor/algs_impl.h`.
    *   Signature: `QR(CMatrix const& M, CMatrix& Q, CMatrix& R, bool complete) -> void`
    *   Same requirements as the real matrix version, but for `CMatrix` inputs and outputs.
    *   Ensure `Q` is unitary rather than orthogonal.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.