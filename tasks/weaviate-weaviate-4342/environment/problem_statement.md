## Description

The vector distance library currently supports SIMD-accelerated implementations of dot product and L2 squared distance using one generation of CPU vector extensions. However, modern server hardware commonly supports a newer, wider generation of these extensions that can process more data per instruction cycle. There are no implementations of dot product or L2 squared distance that take advantage of this wider instruction set, leaving potential performance on the table for users running on capable hardware.

## Expected Behavior

- A new dot product implementation should be available that uses the wider 512-bit vector instruction set when the hardware supports it.
- A new L2 squared distance implementation should be available that similarly uses the wider instruction set.
- Both implementations must produce numerically correct results (within acceptable floating-point tolerance) across the full range of embedding dimensions used in practice — from very small vectors (e.g., 1–6 dimensions) up to large embedding sizes (e.g., 1024, 1536 dimensions), including sizes that are not multiples of 16 or 32.
- The implementations should only be invoked when the CPU supports the required instruction set, maintaining compatibility on older hardware.

## Why This Matters

As embedding model dimensions grow (384, 768, 1536 dimensions are common), the performance of distance calculations becomes a bottleneck in approximate nearest neighbor search. Supporting the wider instruction set extension allows the system to take full advantage of modern CPU capabilities, improving query throughput without sacrificing numerical accuracy.
