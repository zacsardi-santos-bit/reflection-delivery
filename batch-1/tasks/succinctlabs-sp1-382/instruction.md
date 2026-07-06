Update the virtual machine builder to support both base and extension field types as distinct parameters. Implement support for arithmetic operations and equality assertions on extension field values, ensuring existing base field operations remain functional.

*   Modify `VmBuilder` to accept two type parameters: a base field type `F` and an extension field type `EF`.
    *   Ensure the `default()` constructor works with the new two-parameter form.
*   Update `AsmConfig` to be parameterized over both `F` and `EF`.
    *   Ensure `Config` implementation sets the associated `EF` type to the provided `EF` type parameter.
*   Enhance `VmBuilder<F, EF>` to support:
    *   Evaluating and asserting equality on extension field values of type `Ext<F, EF>`.
    *   Performing arithmetic operations (addition, subtraction, multiplication, division) on extension field values.
    *   Ensure `assert_ext_eq` method compiles and executes correctly for all arithmetic operations.
*   Update `compile()` method in `VmBuilder<F, EF>` to produce a `Program` executable by `Runtime<F, EF>`.
    *   Ensure it supports extension field arithmetic and equality assertions.
*   Modify `compile_to_asm()` method in `VmBuilder<F, EF>` to produce `AssemblyCode<F, EF>`.
    *   Ensure it can be converted to machine code and executed by `Runtime<F, EF>`.
*   Adjust `Instruction::new` constructor to accept two additional boolean parameters:
    *   `imm_ext_b` and `imm_ext_c` to indicate if `op_b` and `op_c` are immediate extension field values.
*   Ensure `Runtime` correctly executes:
    *   Extension field arithmetic opcodes (`EADD`, `ESUB`, `EMUL`, `EDIV`).
    *   Extension field branch opcodes (`EBEQ`, `EBNE`) using `imm_ext_b` and `imm_ext_c` flags.
*   Verify existing tests for conditionals and loops using `VmBuilder<F, EF>` continue to compile and run correctly.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.