## Description

The wabt disassembler is producing incomplete and inaccurate output when disassembling binary WebAssembly files that contain certain wide instructions or multi-byte prefix opcodes.

Two display bugs exist in the disassembler:

1. **Truncated instruction bytes**: When an instruction's byte encoding is longer than the per-line display width, the disassembler silently drops the overflow bytes instead of wrapping them to a continuation line. This affects 128-bit vector constant instructions (18 bytes each) and large 64-bit integer constants (11 bytes). Some bytes simply do not appear in the output at all.

2. **Incorrect byte offset and incomplete opcode display for prefix opcodes**: For instructions that begin with a prefix byte (such as atomic memory operations, SIMD operations, and saturating conversions), the displayed byte offset is wrong — it shows the offset of one of the interior opcode bytes rather than the address of the first (prefix) byte. Additionally, for SIMD instructions whose sub-opcode value is >= 128, the sub-opcode occupies two bytes in the binary file due to LEB128 encoding, but the disassembler only shows one of those two bytes. This causes both wrong offsets and missing bytes in the output.

## Expected Behavior

- Every byte of every instruction must appear in the disassembly output, wrapping to additional display rows as needed.
- Each display row must show at most 9 bytes (matching the defined display width constant).
- The byte offset displayed for each instruction must be the address of the first byte of that instruction (the prefix byte), not a later byte.
- Continuation rows must show additional bytes and their correct offsets, but no instruction mnemonic.

## Why This Matters

Developers relying on the disassembler for debugging are seeing truncated output with incorrect byte addresses, making it impossible to accurately correlate disassembly lines with file positions or to verify instruction encodings.
