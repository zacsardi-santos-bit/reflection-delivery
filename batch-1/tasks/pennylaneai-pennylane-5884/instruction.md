Implement a quantum transform that expands multi-term observable measurements into individual single-term measurements within a single circuit execution. Ensure it deduplicates identical terms, handles identity terms as numeric offsets, and is compatible with major automatic differentiation frameworks.

*   Create a new transform function `split_to_single_terms` in `pennylane/transforms/split_to_single_terms.py`.
    *   Export `split_to_single_terms` from `pennylane/transforms/__init__.py`.
    *   Accept a `QuantumScript` tape, a batch of tapes (list or tuple), or a `QNode/Callable`.
    *   Return a tuple `(tapes, postprocessing_fn)`.

*   Handle tapes with no measurements:
    *   Return `((tape,), null_postprocessing)` without modification.

*   Handle tapes with single-term or wire-based measurements:
    *   Return the original tape unchanged with `null_postprocessing`.

*   For tapes with multi-term observables (Sum, Hamiltonian, LinearCombination, ScalarProduct):
    *   Split into individual single-term expectation value measurements on a single output tape.
    *   Deduplicate identical single-term observables across multiple measurements.

*   Implement a postprocessing function:
    *   Reconstruct original multi-term expectation values from single-term results.
    *   Handle scalar coefficients correctly (e.g., 0.5 * Y(0) contributes 0.5 * <Y(0)>).

*   Handle identity terms in multi-term observables as constant offsets without device execution.

*   Export `null_postprocessing` from `pennylane/transforms/split_to_single_terms`.
    *   Ensure it returns the first element of the results batch unchanged.
    *   Ensure identity comparisons like `fn is null_postprocessing` pass.

*   Raise a `RuntimeError` with the message "Cannot split up terms in sums for MeasurementProcess" when:
    *   A Sum or Hamiltonian (or ScalarProduct wrapping a Sum) observable is used in a non-expval measurement type (e.g., counts, sample).

*   For a batch of tapes (list or tuple):
    *   Transform each tape independently.
    *   Return a flat list of transformed tapes with a combined postprocessing function.

*   Ensure compatibility with:
    *   Autograd, JAX (including jit), PyTorch, and TensorFlow for differentiable Hamiltonian coefficients.
    *   Shot vectors (list of shot counts) and batched circuit parameters, maintaining correct output shapes.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.