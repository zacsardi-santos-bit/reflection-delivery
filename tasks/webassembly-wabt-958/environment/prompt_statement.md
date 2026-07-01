I'm working with the wabt binary toolkit and I've found two bugs in the disassembler output.

First, when I disassemble a binary file containing instructions that are many bytes long — like a 128-bit vector constant or the smallest 64-bit integer value — the output cuts off some of the bytes. The disassembler should wrap any overflow bytes onto additional display lines with their correct file offsets, but instead it just silently drops the bytes that don't fit. The output ends up showing fewer bytes than the instruction actually contains in the file.

Second, for any instruction that starts with a prefix byte (the atomic operations, SIMD operations, and saturating conversion instructions all do this), the byte offset shown at the start of the line is wrong — it's off from the actual location of the first byte of the instruction. And for SIMD instructions where the secondary part of the opcode requires two bytes in the file, the display only shows one of those two bytes. So both the offset and the byte count for these instructions are incorrect.

I'd like both issues fixed so the disassembler correctly shows all bytes of every instruction (wrapping to additional lines as needed) and displays the correct file offset pointing to the first byte of each instruction.
