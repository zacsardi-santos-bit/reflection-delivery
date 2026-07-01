Ensure the RISC-V 64-bit backend in Cranelift consistently specifies the rounding mode when converting 32-bit integers to 64-bit double-precision floating-point numbers. Update the code generation to include the round-to-nearest mode for these conversions.

*   Modify the Cranelift compiler to handle the following conversions:
    *   For unsigned 32-bit integer to 64-bit double:
        *   Emit the instruction `fcvt.d.wu` with `rne` rounding mode, formatted as `fcvt.d.wu fa0,a0,rne`.
    *   For signed 32-bit integer to 64-bit double:
        *   Emit the instruction `fcvt.d.w` with `rne` rounding mode, formatted as `fcvt.d.w fa0,a0,rne`.

*   Update the filetest expectations:
    *   Edit `cranelift/filetests/filetests/isa/riscv64/float.clif` to reflect the new VCode output with the `rne` suffix for both conversion instructions.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.