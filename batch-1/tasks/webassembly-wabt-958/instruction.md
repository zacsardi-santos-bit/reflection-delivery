Fix the disassembler in the wabt toolkit to correctly display all bytes of instructions and show accurate byte offsets. Ensure that long instructions wrap correctly across multiple lines and that prefix opcodes display the correct starting byte offset.

*   Modify the disassembler logic in `src/binary-reader-objdump.cc` to handle instruction bytes:
    *   Display all bytes for each instruction, wrapping to continuation lines as needed.
    *   Ensure continuation lines show remaining bytes with correct offsets, without repeating the instruction mnemonic.
    *   Limit each display line to a maximum of 9 bytes, as defined by `IMMEDIATE_OCTET_COUNT`.
    *   For instructions longer than 9 bytes, split them across multiple lines.
*   Correctly compute and display the byte offset:
    *   Show the offset of the first byte of the instruction (e.g., the prefix byte for SIMD or atomic instructions).
    *   For two-byte prefix opcodes, calculate the offset as 2 bytes before the start of the immediates.
*   Ensure the disassembler reads and displays actual binary bytes:
    *   Include multi-byte LEB128 sub-opcode encodings in the output.
    *   For SIMD instructions with sub-opcodes >= 0x80, display all three bytes (e.g., 0xfd 0x84 0x01).
*   Implement changes by updating the disassembly printing logic to:
    *   Loop over instruction bytes in chunks of 9.
    *   Compute the correct starting file offset by subtracting the full opcode length from the current reader position.
    *   Read raw bytes from the binary data buffer at the correct absolute offsets.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.