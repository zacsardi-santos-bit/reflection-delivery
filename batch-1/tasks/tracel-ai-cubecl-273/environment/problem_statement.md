## Description

The batch matrix multiplication layer currently hard-codes how the GPU launch grid's axes map to matrix dimensions. Specifically, the x-axis of the launch grid always corresponds to the row dimension and the y-axis always corresponds to the column dimension. There is no way to override this mapping, which means:

- Workloads where the number of output tiles along rows is small but along columns is large cannot efficiently use a launch grid with more work units along the x-axis.
- Swizzled access patterns — which traverse tiles in a non-linear order to improve cache reuse — are not supported at the batch dispatch level.
- Any algorithm that wants to schedule work with a transposed or swizzled cube grid simply cannot be expressed.

## Expected Behavior

- A pluggable dispatch abstraction should be added to the batch module so that users can control how the launch grid's x, y, and z positions are translated into matrix row, column, and batch indices.
- At minimum, four dispatch strategies should be available: a natural (default) mapping, a transposed mapping, and two swizzled variants parameterized by a swizzle width.
- All four strategies must produce numerically correct matrix multiplication results.
- The batch matmul types that previously operated with a fixed number of generic parameters must now accept the dispatch strategy as an additional type parameter.
- All existing uses of these batch matmul types must continue to work correctly when they supply the natural dispatch strategy.

## Why This Matters

Without this flexibility, it is impossible to launch batch matrix multiplications with non-standard grid orientations. This blocks performance optimization strategies that rely on reordering tile traversal or transposing the grid layout to match memory access patterns on specific hardware.
