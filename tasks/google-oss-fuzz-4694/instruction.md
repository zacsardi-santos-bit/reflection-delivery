Implement a function named `remove_z_defs` in the `compiler_wrapper` module to handle the removal of specific linker flags that enforce strict symbol resolution. Ensure the function adheres to Python naming conventions and correctly processes various flag formats.

*   Implement the `remove_z_defs` function with the following signature:
    *   `remove_z_defs(args: list) -> list`
    *   Located in `infra/base-images/base-sanitizer-libs-builder/compiler_wrapper.py`
*   Ensure the function processes a list of compiler/linker arguments and returns a new list with specific flags removed:
    *   Remove standalone '-Wl,-z,defs' arguments.
    *   Remove standalone '-Wl,--no-undefined' arguments.
    *   Preserve other flags, such as '-Wl,-z,relro'.
    *   In compound '-Wl' arguments, remove only the '-z,defs' sub-option.
    *   Handle split two-token forms, such as '-Wl,-z' followed by '-Wl,defs', removing both tokens even with unrelated arguments between them.
*   Ensure the function preserves all other linker flags, including those unrelated to symbol-definition enforcement.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.