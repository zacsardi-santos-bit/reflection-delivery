## Description

The ITensor library currently supports several tensor decompositions (such as singular value decomposition), but it is missing support for QR decomposition. This is a fundamental linear algebra factorization that many tensor network algorithms rely on, and its absence makes the library incomplete for users who need this capability.

## Expected Behavior

- It should be possible to decompose any tensor into an orthogonal/unitary factor and an upper-triangular factor, analogous to matrix QR decomposition but generalized to multi-index tensors.
- The user should be able to specify which indices of the tensor belong to the "Q" factor; the remaining indices go to the "R" factor.
- Both a "complete" (full, square orthogonal factor) and a "thin/economy" (reduced-size) variant should be supported.
- The decomposition should work correctly for real-valued tensors, complex-valued tensors, and tensors carrying quantum number structure (both zero and non-zero divergence).
- The underlying low-level matrix routine for QR should also be available and handle both real and complex matrices, including tall and rank-deficient cases.

## Why This Matters

QR decomposition is widely used in tensor network computations for orthogonalizing tensor networks efficiently. Without it, users must resort to SVD (which is more expensive), or implement their own ad hoc workarounds. Adding native support improves both performance and usability for common tensor network workflows.
