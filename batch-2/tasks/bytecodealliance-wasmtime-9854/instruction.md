Reorganize the Pulley bytecode interpreter's instruction set by moving specific operations from the regular to the extended opcode tier. Update the encoding and disassembly to reflect these changes while maintaining existing mnemonics.

*   Update the `Op` enum:
    *   Ensure it includes an `ExtendedOp(ExtendedOp)` variant for wrapping extended instructions.

*   Modify the `ExtendedOp` enum:
    *   Include `XPush32Many` and `XPop32Many` as variants.
    *   Ensure these are no longer direct variants of `Op`.

*   Define the `XPush32Many` struct:
    *   Include a `srcs` field of type `RegSet<XReg>`.
    *   Ensure it is constructed as `Op::ExtendedOp(ExtendedOp::XPush32Many(XPush32Many { srcs: RegSet<XReg> }))`.

*   Define the `XPop32Many` struct:
    *   Include a `dsts` field of type `RegSet<XReg>`.
    *   Ensure it is constructed as `Op::ExtendedOp(ExtendedOp::XPop32Many(XPop32Many { dsts: RegSet<XReg> }))`.

*   Adjust bytecode encoding:
    *   Ensure extended op instructions consume an additional byte due to the two-byte opcode prefix.

*   Update disassembly output:
    *   Reflect the updated byte offsets for sequences containing `xpush32_many` and `xpop32_many`.
    *   Maintain existing mnemonics but shift byte offsets due to the encoding change.
    *   Specifically, ensure `xadd64_uoverflow_trap` as an `ExtendedOp` shifts subsequent byte addresses in disassembly.

*   Modify macro usage in `pulley/src/lib.rs`:
    *   Move `XPush32Many`, `XPop32Many`, and `xadd64_uoverflow_trap` from `for_each_op!` to `for_each_extended_op!`.

*   Update macro-generated code in:
    *   `pulley/src/op.rs`
    *   `pulley/src/encode.rs`
    *   `pulley/src/decode.rs`
    *   `pulley/src/interp.rs`

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.