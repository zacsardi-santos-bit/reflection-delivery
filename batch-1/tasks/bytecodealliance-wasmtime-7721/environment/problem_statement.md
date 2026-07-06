## Description

The Winch baseline compiler is missing support for several WebAssembly memory management instructions. When a WebAssembly module uses operations to bulk-fill a memory region with a constant byte value, initialize memory from a data segment, copy regions of memory, query the current memory size in pages, drop a data segment, or dynamically grow memory, attempting to compile and run that module with the Winch compiler will fail or produce incorrect results.

## Expected Behavior

- Filling a memory region with a byte value should succeed when the target range is within bounds and trap with an out-of-bounds memory access error when it is not.
- Initializing memory from a data segment should succeed for valid offsets and segment indices, and trap with an appropriate error for out-of-bounds accesses. Attempting to use these instructions in a module with no declared memory or an invalid data segment index should be rejected at validation time.
- Dropping a data segment should be supported, after which attempting to initialize memory from the dropped segment should trap.
- Copying a memory region from one address to another should work correctly.
- Querying the current memory size should return the correct page count and update to reflect any subsequent growth operations.
- Growing memory should return the previous page count on success and -1 when growth would exceed the maximum. It should be usable as an expression in any operand position, including branch conditions, function call arguments, local variable assignments, and arithmetic.
- Module validation should reject incorrect operand types for all of these instructions with a type mismatch error.

## Why This Matters

Without support for these instructions, Winch cannot compile many real-world WebAssembly programs. Adding correct implementations unblocks a significantly larger class of modules from running under the Winch compiler and makes it a more complete and viable execution tier.
