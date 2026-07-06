## Description

xarray currently lacks a memory-efficient index type for regularly-spaced floating-point coordinate arrays. When users work with uniform grids — which are extremely common in scientific computing (e.g., time axes, spatial grids) — they must materialize the full array of coordinate values even though such arrays can be completely described by just a start, stop, and step value.

This issue tracks the addition of a new built-in index type that represents an arithmetic range without storing every element. The index should behave like the standard range-generating constructors (step-based and count-based variants), but as a first-class xarray index that understands range arithmetic for slicing and label-based selection.

## Expected Behavior

- A new range-based index type should be available in xarray's indexes module, constructible via step-based (start/stop/step) and count-based (start/stop/num-points) factory methods.
- The index should expose start, stop, and step properties.
- Integer-based slicing should return a new range index with updated bounds, rather than materializing the full array.
- Label-based selection should support nearest-neighbor lookup for scalars, arrays, and slices.
- Renaming coordinates or dimensions on a dataset backed by this index should preserve the index type.
- Converting to a standard in-memory index should work for compatibility with existing code.
- Additionally, the comparison utility for approximate equality should be extended to support standalone coordinate collection objects (not just datasets, data arrays, and variable objects).
- Datasets using coordinate-transform-based indexes should survive a rename-and-back round-trip and compare as identical to the original.

## Why This Matters

Memory savings are significant for large uniform grids where storing the full floating-point array is expensive but unnecessary. Range-aware slicing also enables more precise arithmetic operations on coordinate values without floating-point accumulation errors that occur when slicing materialized arrays.
