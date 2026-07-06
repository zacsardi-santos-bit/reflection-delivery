## Description

Small, constant-length bulk memory copy operations in WebAssembly currently always go through a library function call, even when the number of bytes to copy is known at compile time and is very small (e.g., 16 or 28 bytes). The per-call overhead of crossing the host/guest boundary and performing an indirect call dominates the actual work for tiny copies, making them significantly slower than they need to be.

## Expected Behavior

When the compiler can determine at compile time that a bulk copy operation covers a small, fixed number of bytes (up to 128 bytes), it should expand that copy directly inline as a sequence of load and store instructions rather than delegating to a library function. The strategy should:

- Cover the byte range with the widest possible load/store granularity (e.g., 16-byte vector loads first, then 8-byte, 4-byte, 2-byte, 1-byte as needed)
- Emit all loads before any stores, so that overlapping source/destination ranges copy correctly (preserving move semantics)
- Use little-endian memory access flags for all chunk operations, ensuring correctness on big-endian targets where vector load/store only supports a little-endian encoding

Copies larger than 128 bytes, or copies with a dynamically computed length, should continue to use the existing library call path.

## Why This Matters

- Tiny constant-length copies are common in GC array manipulation and linear-memory operations. Eliminating the library call overhead gives measurable speedups (roughly 1.7–2.7× faster than the libcall path for small sizes on measured hardware).
- The optimization must work correctly for all element types (integer, float, SIMD vector) and must handle same-array overlapping copies without corruption.
- Big-endian targets (e.g., big-endian Pulley) previously triggered an internal assertion when vector instructions were emitted with the wrong endianness; pinning all chunk accesses to little-endian fixes this regression.
