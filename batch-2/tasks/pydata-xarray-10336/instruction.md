Implement automatic chunk alignment for writing chunked data arrays to a Zarr store using xarray. Create utility functions to compute grid-aligned chunk layouts and update error messages for consistency.

*   Create a new module at `xarray/backends/chunks.py` with the following functions:
    *   `build_grid_chunks(size: int, chunk_size: int, region: slice | None) -> tuple[int, ...]`
        *   Return a tuple of chunk sizes covering the dimension.
        *   Handle cases where `region` is `None` or has no effective offset by returning standard chunks.
        *   Adjust the first chunk for non-zero start offsets to align with grid boundaries.
    *   `align_nd_chunks(nd_var_chunks: tuple[tuple[int, ...], ...], nd_backend_chunks: tuple[tuple[int, ...], ...]) -> tuple[tuple[int, ...], ...]`
        *   Return aligned chunks per dimension, reflecting alignment between variable and backend chunks.
    *   `grid_rechunk(variable: xarray.Variable, enc_chunks: tuple[int, ...], region: tuple[slice, ...]) -> xarray.Variable`
        *   Rechunk a dask-backed xarray Variable to align with `enc_chunks` and `region`.
        *   Ensure the returned Variable's `.chunks` attribute matches expected aligned chunk tuples.
        *   Support multi-dimensional arrays.

*   Update the `xarray.DataArray.to_zarr()` method:
    *   Add an `align_chunks` keyword argument (boolean, default `False`).
    *   When `align_chunks=True` and encoding specifies chunk sizes, ensure data writes successfully and roundtrips correctly.

*   Update error messages for chunk alignment issues:
    *   Include the substring 'would overlap multiple Dask chunks' with a capital 'D' in Dask.
    *   Include the substring 'Specified Zarr chunks' with a capital 'Z' in Zarr.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.