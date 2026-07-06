## Description

The Cairo compiler's output for dictionary operations contains inconsistently formatted hint blocks. When the compiler generates low-level assembly code for programs that use dictionary data structures, it embeds Python-style hint blocks that guide the prover during execution. Currently, these hint blocks have incorrect indentation levels and contain extra trailing spaces on individual lines, causing the generated output to not match the expected format.

## Expected Behavior

- Multi-line hint blocks embedded in the compiled assembly output should use consistent indentation aligned with the surrounding code (4 indent levels, not 5)
- Individual lines within hint blocks should not have trailing whitespace
- Single-line hints should have exactly one space of padding between the hint delimiters and the hint content
- The hint open and close delimiter tokens should be placed directly adjacent to their content, without extra surrounding spaces

## Why This Matters

The compiler's output format is critical because downstream tools and test infrastructure rely on exact string matching of the generated code. When the formatting is incorrect, automated verification of the compiler's output fails. Fixing the formatting ensures that the dictionary compilation pipeline produces output that exactly matches the expected canonical form, allowing integration tests to pass and ensuring the generated code can be correctly interpreted by the Cairo virtual machine and its associated tooling.
