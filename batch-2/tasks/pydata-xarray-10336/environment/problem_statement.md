## Description

When writing a chunked data array to a Zarr store with a specified chunk encoding that differs from the array's existing chunk layout, xarray currently raises an error or produces silently incorrect results rather than automatically aligning the chunks. Users who have arrays with irregular or non-aligned chunk sizes must manually rechunk their data before writing, which is inconvenient and error-prone.

## Expected Behavior

- When writing a chunked data array to Zarr with explicit chunk encoding, users should be able to request automatic alignment of the array's chunks to match the encoding's chunk grid.
- There should be utility functions for computing grid-aligned chunk layouts for a single dimension given a target chunk size and an optional region (slice), for aligning multi-dimensional variable chunks with backend chunk specifications, and for rechunking a variable's underlying data to align with encoding chunks and a region.
- These chunk-alignment utilities should correctly handle multi-dimensional arrays, various region offsets, and irregular variable chunk sizes.

## Why This Matters

Without automatic chunk alignment, users writing chunked arrays to Zarr must manually rechunk arrays before saving — adding extra steps, potential memory overhead, and cognitive burden. Automating this process makes the write path more robust and user-friendly. Additionally, error messages referring to library names should use consistent capitalization as proper nouns.
