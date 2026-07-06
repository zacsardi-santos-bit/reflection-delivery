## Description

The Pulley bytecode interpreter uses a two-tier instruction encoding: regular instructions with a single-byte opcode and extended instructions that use a two-byte opcode prefix. Some instructions that are rarely executed in the hot path—such as stack push/pop operations and overflow-checked arithmetic—currently live in the regular instruction set but should be moved to the extended instruction set.

Moving these instructions to the extended tier is important for opcode space management: the regular instruction set has a fixed opcode budget, and reserving those opcodes for the most common instructions improves the efficiency of the overall encoding.

## Expected Behavior

- Stack register push/pop operations (single and multi-register variants) should be encoded as extended instructions, wrapped inside the extended op variant of the main instruction enum.
- Overflow-checked arithmetic operations should similarly move to the extended instruction category.
- Disassembly output for functions containing these operations should reflect the updated byte offsets: because extended ops take one more byte than regular ops, all subsequent instructions appear at higher byte addresses.
- Existing disassembly mnemonics (the human-readable names) for these operations should remain unchanged—only the byte-level encoding and offset calculations change.
- Code that constructs or pattern-matches on these instructions must use the extended op wrapper form.

## Why This Matters

As the Pulley instruction set grows, it is important to carefully curate which instructions occupy the limited regular opcode space. Reorganizing less-frequently-used instructions into the extended opcode space keeps the common-case fast path compact.
