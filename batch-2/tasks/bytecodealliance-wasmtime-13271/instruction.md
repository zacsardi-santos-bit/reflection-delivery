I'm working on the Winch compiler's AArch64 backend in the Wasmtime project.

*   The Winch compiler's AArch64 backend must successfully compile WebAssembly modules that use the 128-bit integer addition operation (consuming four i64 values representing two 128-bit integers as lo/hi pairs and producing two i64 values as the 128-bit result) when the wide arithmetic proposal is enabled.

*   For 128-bit integer addition on AArch64, the generated assembly must use the carry-setting add instruction for the low 64-bit word and the add-with-carry instruction for the high 64-bit word.

*   The Winch compiler's AArch64 backend must successfully compile WebAssembly modules that use the 128-bit integer subtraction operation (consuming four i64 values representing two 128-bit integers as lo/hi pairs and producing two i64 values as the 128-bit difference) when the wide arithmetic proposal is enabled.

*   For 128-bit integer subtraction on AArch64, the generated assembly must use the borrow-setting subtract instruction for the low 64-bit word and the subtract-with-carry instruction for the high 64-bit word.

*   The Winch compiler's AArch64 backend must successfully compile WebAssembly modules that use the signed 64×64→128-bit multiply-wide operation (consuming two i64 values and producing two i64 values representing the full signed 128-bit product as lo/hi) when the wide arithmetic proposal is enabled.

*   For signed multiply-wide on AArch64, the generated assembly must use the signed-multiply-high instruction to produce the high 64 bits and the regular multiply instruction for the low 64 bits.

*   The Winch compiler's AArch64 backend must successfully compile WebAssembly modules that use the unsigned 64×64→128-bit multiply-wide operation (consuming two i64 values and producing two i64 values representing the full unsigned 128-bit product as lo/hi) when the wide arithmetic proposal is enabled.

*   For unsigned multiply-wide on AArch64, the generated assembly must use the unsigned-multiply-high instruction to produce the high 64 bits and the regular multiply instruction for the low 64 bits.

*   All four wide arithmetic operations must work correctly regardless of how the operands are provided: as immediate constants, as local variables, or as function parameters.

*   The wide arithmetic WebAssembly proposal must no longer be listed as unsupported for Winch on AArch64, so that modules using these instructions can be compiled without being rejected.


*   Interface details: NO INTERFACES NEEDED

The tests are WebAssembly disassembly snapshot tests (`.wat` files) that verify the Winch compiler generates correct AArch64 assembly for wide arithmetic WebAssembly instructions. They do not call Rust functions or classes by specific names. The implementation requires modifying existing internal methods within the Winch AArch64 backend, specifically in `winch/codegen/src/isa/aarch64/masm.rs` (to implement the `add128`, `sub128`, and `mul_wide` trait methods for AArch64) and `crates/wasmtime/src/config.rs` (to remove the restriction treating wide arithmetic as unsupported on AArch64 with Winch). No new public API symbols are introduced.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.