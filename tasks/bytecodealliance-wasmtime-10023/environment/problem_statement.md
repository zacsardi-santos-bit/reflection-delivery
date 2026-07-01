## Description

The Winch JIT compiler currently supports a subset of WebAssembly atomic read-modify-write (RMW) operations for shared memory — specifically addition, subtraction, and exchange. However, support for the three bitwise atomic RMW operations (AND, OR, and XOR) is missing.

This means that any WebAssembly module that uses atomic bitwise operations on shared memory values cannot be compiled by Winch today. The WebAssembly threads specification defines these operations as part of the standard atomic instruction set, and the other major compiler backends already support them. Without this support in Winch, multi-threaded WebAssembly programs that manipulate individual bits in shared memory (a common pattern in synchronization primitives and concurrent data structures) will fail.

## Expected Behavior

- Winch's x86-64 backend should correctly compile atomic AND read-modify-write operations for both 32-bit and 64-bit integer types, including sub-word (8-bit and 16-bit) unsigned variants.
- Winch's x86-64 backend should correctly compile atomic OR read-modify-write operations for both 32-bit and 64-bit integer types, including sub-word unsigned variants.
- Winch's x86-64 backend should correctly compile atomic XOR read-modify-write operations for both 32-bit and 64-bit integer types, including sub-word unsigned variants.
- All aligned operations should include proper alignment checks that trap on misaligned addresses, consistent with behavior of the already-supported atomic operations.

## Why This Matters

Without these operations, Winch cannot execute a large class of valid multi-threaded WebAssembly programs. Adding support completes the set of standard atomic RMW operations in the Winch x86-64 backend, bringing it to parity with the specification and other backends.
