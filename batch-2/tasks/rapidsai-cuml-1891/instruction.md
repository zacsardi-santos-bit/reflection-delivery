Implement enhancements to the distributed computation utilities for multi-GPU machine learning pipelines. Update the existing hierarchical tree-reduction helper to accept a combining function and handle distributed futures. Add a new flat-reduction utility that aggregates results from distributed futures using a specified function.

*   Update the `tree_reduce` function in `python/cuml/dask/common/func.py`:
    *   Accept an optional `func` parameter, defaulting to summation.
    *   Use the provided `func` as a delayed callable for combining elements during reduction.
    *   Ensure compatibility with lists of distributed futures (e.g., from `client.submit`), not just dask delayed objects.
    *   Return a computable result that sums all values when called without a `func` on integer futures.
    *   Internally wrap plain Python callables passed as `func` into delayed callables.

*   Implement a new `reduce` function in `python/cuml/dask/common/func.py`:
    *   Accept a list of distributed futures as the first argument.
    *   Accept a plain Python callable (e.g., `sum`) as the second argument.
    *   Include an optional `client` keyword argument, defaulting to the active distributed client.
    *   Return a computable object (future or delayed) that, when executed with `client.compute(result, sync=True)`, yields the result of applying the callable to all input values.
    *   Ensure `reduce([future_0, future_1, ..., future_{n-1}], sum)` produces a computable equal to `sum(range(n))`.

*   Ensure both `tree_reduce` and `reduce` functions produce correct results for partition counts of 1, 2, 10, and 15.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.