Implement a new memory-efficient index type, `RangeIndex`, for xarray that represents regularly-spaced floating-point coordinate arrays without storing every element. Ensure it behaves like standard range-generating constructors and supports slicing and label-based selection.

*   Implement `RangeIndex` in `xarray/indexes/range_index.py` and ensure it is importable alongside `PandasIndex`.
*   Provide `RangeIndex.arange` class method:
    *   Accept positional args like numpy's arange: one arg as stop; two as (start, stop); three as (start, stop, step).
    *   Accept keyword args: start, stop, step, with `dim` as a required keyword-only argument, `coord_name` optional (defaults to `dim`), and `dtype` optional.
    *   Raise `TypeError` with message matching ".*requires stop to be specified" if no stop is provided.
    *   Interpret `start` as stop when passed as a keyword argument alone.
*   Provide `RangeIndex.linspace` class method:
    *   Accept `start`, `stop`, `num`, `endpoint`, and `dim` arguments.
    *   Calculate `step` as `(stop-start)/num` when `endpoint=False`.
    *   Set `stop` property as `start + num * step` when `endpoint=True`.
*   Expose `start`, `stop`, and `step` as readable properties.
*   Implement `isel` method:
    *   Return a new `RangeIndex` with updated bounds for slice indexers.
    *   Return `None` for scalar integer indexers.
    *   Return `PandasIndex` for 1-d array or 1-d Variable indexers.
*   Implement `sel` method:
    *   Support only `method='nearest'`, raising `ValueError` otherwise.
    *   Raise `ValueError` if `tolerance` is specified.
    *   Support scalar, list, `xr.Variable`, `xr.DataArray`, and slice label inputs.
*   Ensure `ds.set_xindex('<coord>', RangeIndex)` raises `NotImplementedError`.
*   Ensure `ds.indexes['<coord>']` returns a pandas Index equivalent to `pd.Index(np.arange(start, stop, step))`.
*   Preserve `RangeIndex` type on coordinate or dimension renaming.
*   Implement `__repr__` and `_repr_inline_` methods with specific formatting.
*   Extend `assert_allclose` utility to accept `xr.Coordinates` objects.
*   Ensure datasets with `CoordinateTransformIndex` survive a rename round-trip and compare identically to the original.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.