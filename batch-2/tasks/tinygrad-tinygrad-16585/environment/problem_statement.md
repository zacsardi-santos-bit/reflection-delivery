## Description

Assigning new values to a slice/view of an already-realized tensor does not work correctly. When you take a sub-range of a realized tensor and try to assign computed values back to that slice, the parent tensor is not updated as expected. This is a common pattern when, for example, updating only part of a tensor in-place (like a KV cache update or a partial buffer write).

## Expected Behavior

- Taking a contiguous slice of a realized tensor and assigning a new value to that slice should update only the slice's region in the parent tensor, leaving the rest of the parent unchanged.
- A simple slice assignment where the computation reads from the same slice being written should require only a single computation step (no unnecessary intermediate copies).
- When two slices of the same parent tensor overlap (source and destination share some elements), the assignment should still produce the mathematically correct result, using an additional step to handle the hazard.
- Reversed/flipped views should similarly support correct in-place assignment.

## Why This Matters

In-place partial updates to tensors are critical for memory-efficient workloads such as KV caches in language models. If slice-based assignment silently produces incorrect results or uses unnecessary computation steps, users cannot rely on this operation for performance-sensitive code paths.
