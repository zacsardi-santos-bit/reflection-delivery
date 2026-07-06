Implement support for several WebAssembly memory management instructions in the Winch baseline compiler. Ensure that operations such as filling, initializing, copying, dropping, querying, and growing memory are correctly handled according to the WebAssembly specification. Validate modules for correct operand types and memory/data segment references.

*   Implement the `memory.fill` instruction:
    *   Ensure filling within bounds succeeds.
    *   Trap with 'out of bounds memory access' when filling beyond memory boundaries.
    *   Allow zero-length fills at the page boundary but trap when beyond memory size.
    *   Support sequential `memory.fill` operations.

*   Implement the `memory.grow` instruction:
    *   Return the previous page count on success.
    *   Return -1 (0xFFFFFFFF as i32) when growth exceeds the maximum or is too large.
    *   Allow the result to be used in any expression context.

*   Implement the `memory.init` instruction:
    *   Succeed when initializing from a valid data segment and offset.
    *   Trap with 'out of bounds memory access' for invalid accesses.
    *   Reject when no memory is declared with 'unknown memory 0'.
    *   Reject invalid data segment indices with 'unknown data segment N'.
    *   Reject non-i32 operand types with 'type mismatch'.

*   Implement the `data.drop` instruction:
    *   Ensure subsequent `memory.init` using a dropped segment traps with 'out of bounds memory access'.

*   Implement the `memory.copy` instruction:
    *   Ensure correct copying between memory regions.
    *   Support combinations with `memory.init` and `data.drop`.

*   Implement the `memory.size` instruction:
    *   Return current memory size in pages as an i32.
    *   Reflect any growth from `memory.grow`.
    *   Reject non-i32 result types with 'type mismatch'.

*   Ensure module validation:
    *   Reject incorrect operand types for all instructions with 'type mismatch'.
    *   Handle multi-byte LEB encoding for data segment indices.

*   Ensure compatibility with both standard and pooling allocator configurations.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.