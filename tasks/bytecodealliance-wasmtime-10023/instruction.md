Implement support for bitwise atomic read-modify-write operations (AND, OR, XOR) on shared memory in the Winch JIT compiler's x86-64 backend. Ensure these operations are correctly compiled for all standard integer widths and handle alignment checks appropriately.

*   Implement atomic AND read-modify-write operations:
    *   Support 8-bit unsigned AND for i32, 16-bit unsigned AND for i32, 32-bit AND for i32.
    *   Support 8-bit unsigned AND for i64, 16-bit unsigned AND for i64, 32-bit unsigned AND for i64, 64-bit AND for i64.

*   Implement atomic OR read-modify-write operations:
    *   Support 8-bit unsigned OR for i32, 16-bit unsigned OR for i32, 32-bit OR for i32.
    *   Support 8-bit unsigned OR for i64, 16-bit unsigned OR for i64, 32-bit unsigned OR for i64, 64-bit OR for i64.

*   Implement atomic XOR read-modify-write operations:
    *   Support 8-bit unsigned XOR for i32, 16-bit unsigned XOR for i32, 32-bit XOR for i32.
    *   Support 8-bit unsigned XOR for i64, 16-bit unsigned XOR for i64, 32-bit unsigned XOR for i64, 64-bit XOR for i64.

*   Ensure alignment checks are included in the generated code:
    *   No alignment check for 8-bit operations.
    *   2-byte alignment check for 16-bit operations.
    *   4-byte alignment check for 32-bit operations.
    *   8-byte alignment check for 64-bit operations.

*   Use a compare-and-swap loop for generating x86-64 machine code:
    *   Load the current memory value.
    *   Apply the bitwise operation.
    *   Attempt a compare-and-exchange with a lock prefix.
    *   Retry on failure.
    *   Match the instruction width with the operation width: byte (8-bit), word (16-bit), dword (32-bit), quadword (64-bit).

*   Organize test files for disassembly:
    *   Place AND operation tests in `tests/disas/winch/x64/atomic/rmw/and/`.
    *   Place OR operation tests in `tests/disas/winch/x64/atomic/rmw/or/`.
    *   Place XOR operation tests in `tests/disas/winch/x64/atomic/rmw/xor/`.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.