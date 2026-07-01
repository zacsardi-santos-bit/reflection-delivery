Update the cxx library's procedural macro to generate Rust code that complies with modern Rust compiler requirements, ensuring compatibility with both newer and older compiler versions. Implement changes to address warnings related to foreign function declaration blocks and safety-implying export attributes.

*   Modify the code generator to:
    *   Emit explicit `unsafe` markers for `extern` blocks on Rust compiler versions 1.82 and later.
    *   Use the `unsafe(...)` wrapper syntax for safety-implying export attributes on Rust compiler versions 1.82 and later.
*   Ensure that the code generator conditionally applies these changes based on the detected Rust compiler version to maintain backward compatibility with versions before 1.82.
*   Verify that the generated code compiles without warnings under the `rust_2024_compatibility` lint group, even when warnings are treated as errors.
*   Confirm that all 24 existing FFI bridge tests compile and pass successfully after implementing the updates.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.