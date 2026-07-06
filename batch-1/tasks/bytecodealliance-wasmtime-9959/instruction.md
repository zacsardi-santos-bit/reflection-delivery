Implement support for the SIMD byte shuffle instruction in the Winch compiler backend for x86_64 architecture. Ensure that the compilation succeeds without errors when using this instruction and that the generated native code correctly performs the byte shuffle operation using vector instructions. Update the test infrastructure to recognize and handle the new SIMD lane shuffle test suite file appropriately.

*   Implement the SIMD byte shuffle instruction in the Winch compiler backend for x86_64:
    *   Ensure the `shuffle` method in the `MacroAssembler` trait is declared with the signature: `fn shuffle(&mut self, dst: WritableReg, lhs: Reg, rhs: Reg, lanes: [u8; 16]) -> Result<()>`.
    *   Implement the `shuffle` method in `winch/codegen/src/isa/x64/masm.rs`:
        *   Check for AVX support and return `CodeGenError::UnimplementedForNoAvx` if not available.
        *   Use two `vpshufb` instructions with per-operand byte masks.
        *   Combine results with a `vpor` instruction.
    *   Provide a stub `shuffle` implementation in `winch/codegen/src/isa/aarch64/masm.rs` that returns `CodeGenError::unimplemented_masm_instruction()`.

*   Update the x64 assembler:
    *   Add the `xmm_vpshufb_rrm` method in `winch/codegen/src/isa/x64/asm.rs` to emit a `vpshufb` instruction.
    *   Add the `vpor` method in `winch/codegen/src/isa/x64/asm.rs` to emit a `vpor` instruction.

*   Modify the WebAssembly visitor:
    *   Implement `visit_i8x16_shuffle` in `winch/codegen/src/visitor.rs` to handle the `i8x16.shuffle` instruction.
    *   Remove `I8x16Shuffle` from the `def_unsupported` exclusion list.
    *   Ensure `TypedReg` has a `v128(reg: Reg)` constructor.

*   Create a new disassembly test file at `tests/disas/winch/x64/i8x16_shuffle/const_avx.wat`:
    *   Specify target `x86_64`, test `winch`, and the `-Ccranelift-has-avx` flag.
    *   Include a WAT module that loads two `v128` constants and applies `i8x16.shuffle`.
    *   Ensure the expected disassembly includes specific instruction sequences.

*   Create a new WAST test file at `tests/misc_testsuite/winch/_simd_lane.wast`:
    *   Include the header `;;! simd = true`.
    *   Define at least 7 exported shuffle functions covering various patterns.
    *   Add the test file path to the unsupported test list in `crates/wast-util/src/lib.rs` for AVX/AVX2 feature detection.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.