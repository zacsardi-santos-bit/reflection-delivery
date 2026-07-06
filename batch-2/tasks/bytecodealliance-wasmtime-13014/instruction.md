I've been looking at a correctness and security issue in the Winch baseline compiler's code generation for memory access instructions on 64-bit platforms.

*   When a 32-bit WebAssembly memory address is used for a load or store operation on a 64-bit platform, the Winch compiler backend must zero-extend the 32-bit address to 64-bit before computing the final memory address — not sign-extend it.

*   On AArch64, all memory address computation instructions for 32-bit WebAssembly heaps must use the 32-bit register form (wN) with the unsigned extend word operation (uxtw), instead of the 64-bit register form (xN) with unsigned extend 64-bit (uxtx). This applies to loads, stores, and atomic operations.

*   On x64, before using a 32-bit value as the offset in a 64-bit memory address computation (addq), the compiler must emit an explicit instruction that zero-extends the 32-bit value to 64-bit (e.g., movl %reg32, %reg32). This applies to loads, stores, atomic loads, atomic stores, atomic read-modify-write operations, and all memory-accessing instructions.

*   A WebAssembly module that loads a signed 8-bit value of -1 (0xFF) from memory and then uses that sign-extended i32 value (-1, or 0xFFFFFFFF unsigned) as the base address for a subsequent memory load with an offset must trap with 'out of bounds memory access' rather than succeeding.

*   A WebAssembly module that uses the return value of a table.grow operation (which is -1 if the grow fails) as a memory store address with any offset must trap with 'out of bounds memory access' rather than succeeding.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.