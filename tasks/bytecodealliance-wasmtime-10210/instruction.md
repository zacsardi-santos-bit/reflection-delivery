Implement support for SIMD operations in the Winch JIT compiler, specifically for checking if all elements in a packed integer vector are non-zero and for extracting a bitmask from the sign bits of each element. Ensure these operations work for all integer vector lane widths (8-bit, 16-bit, 32-bit, and 64-bit elements) and take advantage of hardware-accelerated instructions when available.

*   Implement the `v128_all_true` method in `winch/codegen/src/masm.rs`:
    *   Signature: `v128_all_true(&mut self, src: Reg, dst: WritableReg, size: OperandSize) -> Result<()>`
    *   Set `dst` to 1 if all lanes in the `src` vector register are non-zero, otherwise set `dst` to 0.
    *   Implement for the x64 backend; return "unimplemented" error for the aarch64 backend.
    *   Ensure correctness for vectors loaded from memory and those created by splatting a scalar value.

*   Implement the `v128_bitmask` method in `winch/codegen/src/masm.rs`:
    *   Signature: `v128_bitmask(&mut self, src: Reg, dst: WritableReg, size: OperandSize) -> Result<()>`
    *   Extract the high bit of each lane in `src` and produce a scalar bitmask in `dst`.
    *   Implement for the x64 backend; return "unimplemented" error for the aarch64 backend.

*   Implement helper methods in `winch/codegen/src/codegen/context.rs`:
    *   `v128_all_true_op<F, M>(&mut self, masm: &mut M, emit: F) -> Result<()>`
    *   `v128_bitmask_op<F, M>(&mut self, masm: &mut M, emit: F) -> Result<()>`
    *   Both methods should pop a v128 register, allocate a GPR destination, call the `emit` closure, free the source register, and push the result as an i32 typed register onto the stack.

*   Implement assembler methods in `winch/codegen/src/isa/x64/asm.rs`:
    *   `xmm_vpmovmsk_rr(&mut self, src: Reg, dst: WritableReg, src_size: OperandSize, dst_size: OperandSize)`
    *   `xmm_vmovskp_rr(&mut self, src: Reg, dst: WritableReg, src_size: OperandSize, dst_size: OperandSize)`

*   Update visitor functions in `winch/codegen/src/visitor.rs`:
    *   Implement `visit_i8x16_all_true`, `visit_i16x8_all_true`, `visit_i32x4_all_true`, `visit_i64x2_all_true`
    *   Implement `visit_i8x16_bitmask`, `visit_i16x8_bitmask`, `visit_i32x4_bitmask`, `visit_i64x2_bitmask`
    *   Dispatch to the corresponding context helper with the appropriate `OperandSize`.
    *   Remove these operations from the `def_unsupported!` macro exclusion list.

*   Ensure the Winch x64 compiler uses VEX-encoded instructions for AVX-enabled hardware:
    *   For 8-bit-element all-true checks, use `vpcmpeqb` and `vptest`.
    *   For 16-bit-element all-true checks, use `vpcmpeqw`.
    *   For 32-bit-element all-true checks, use `vpcmpeqd`.
    *   For 64-bit-element all-true checks, use `vpcmpeqq`.
    *   For 8-bit-element bitmask extraction, use `vpmovmskb`.
    *   For 16-bit-element bitmask extraction, use `vpacksswb` and `vpmovmskb`.
    *   For 32-bit-element bitmask extraction, use `vmovmskps`.
    *   For 64-bit-element bitmask extraction, use `vmovmskpd`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.