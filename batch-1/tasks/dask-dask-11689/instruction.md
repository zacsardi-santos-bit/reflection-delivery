Implement an internal expression-based array backend within the Dask package to support creating random distributed arrays and performing arithmetic operations. Ensure the test infrastructure can handle tests specific to this new backend with a dedicated command-line option.

*   Create the `dask.array._array_expr` module:
    *   Ensure it is an importable Python package within the Dask source tree.
    *   Include a `random` submodule accessible as `dask.array._array_expr.random`.

*   Develop the `random` submodule:
    *   Provide a `random` function with the signature `random(shape: tuple, chunks: tuple) -> Array`.
    *   Ensure the function returns an array collection object.

*   Implement the `Array` class in `dask.array._array_expr._collection`:
    *   Support arithmetic operators: `__add__`, `__sub__`, `__mul__`, `__truediv__`, `__floordiv__`, `__pow__`, `__radd__`, `__rsub__`, `__rmul__`, `__rtruediv__`, `__rfloordiv__`, `__rpow__`.
    *   Ensure operations produce results numerically equal to equivalent operations on computed numpy arrays.
    *   Implement a `.compute()` method to return the computed numpy array.

*   Update the pytest configuration:
    *   Register an `array_expr` marker.
    *   Add a `--runarrayexpr` command-line option.
    *   Ensure tests marked with `array_expr` are skipped unless `--runarrayexpr` is passed.
    *   Skip non-`array_expr` tests when `--runarrayexpr` is passed.

*   Ensure the `Array` class is importable directly from `dask.array`.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.