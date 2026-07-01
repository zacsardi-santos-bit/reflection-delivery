## Description

The current Solidity ABI encoding implementation always allocates a new buffer on every call, returning the encoded bytes as a heap-allocated vector. This is wasteful when the caller already knows the output size and has a pre-allocated buffer ready — for example, in embedded or constrained environments, or when building larger buffers incrementally.

We should add a non-allocating variant of the encoding API that accepts a pre-allocated mutable byte slice, writes the encoded data directly into it, and returns the number of bytes written. This write-to-buffer capability should be available consistently at every level of the encoding hierarchy: for individual types, for the general encoding trait, and for parameter sequence encoding (including the convenience free function wrapper).

Additionally, encoding and decoding for nested collection types — nested fixed-size arrays, nested dynamic arrays, and nested tuples — should be fully supported and correct.

## Expected Behavior

- The encoding API should offer a buffer-writing variant alongside the existing allocating variant at all levels.
- The buffer-writing variant must return the number of bytes written.
- The bytes written by the buffer variant must exactly match the bytes that the allocating variant would have returned.
- For parameter sequence encoding, the behavior around top-level offsets for dynamic types must be consistent between the allocating and non-allocating variants.
- Nested fixed-size arrays, nested dynamic arrays, and nested tuples must encode and decode correctly, producing output compatible with the standard Solidity ABI encoding format.

## Why This Matters

Callers that know the encoded size in advance should be able to avoid unnecessary allocations by writing directly into a pre-existing buffer. This is a common performance pattern and important for use in constrained runtime environments.
