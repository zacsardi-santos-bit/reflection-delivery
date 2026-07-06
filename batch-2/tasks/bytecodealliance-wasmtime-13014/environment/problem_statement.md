## Description

The Winch baseline compiler incorrectly handles 32-bit WebAssembly memory addresses on 64-bit platforms. When computing a final memory address, the compiler treats a 32-bit integer offset as if it were already a 64-bit value rather than properly zero-extending it. This creates a correctness and security issue: a 32-bit value that is negative when interpreted as a signed integer (such as -1, stored as 0xFFFFFFFF) gets treated as if it is already a large positive 64-bit number, which can bypass memory bounds checks and allow out-of-bounds access.

## Expected Behavior

- When a 32-bit WebAssembly address value is used in a memory load or store, the compiler must always zero-extend it to 64-bit before adding it to the memory base address.
- A program that reads a signed byte (-1) from memory and uses that value as a base for a subsequent memory access should always trap with an out-of-bounds error, not succeed.
- A program that uses the result of a failed table grow operation (which returns -1 on failure) as a memory store address should also trap with an out-of-bounds error.

## Why This Matters

Without this fix, WebAssembly programs can craft inputs that cause the Winch compiler to generate code that reads or writes outside the bounds of the allocated linear memory. Two concrete exploit patterns are affected: using a sign-extended byte load result as a memory address, and using the -1 return value of a failed table grow as a store address. Both patterns should reliably trap, not silently access arbitrary memory.
