## Description

Several core traits in the code analysis library are defined without explicit lifetime parameters, but the AST node types they work with require those lifetimes to be tracked correctly. As a result, the entire minifier crate fails to compile.

Specifically, two traits that deal with global reference checking and side-effect analysis context need to have an explicit lifetime parameter added. Without this, it is impossible to properly express that the identifier reference objects passed into these trait methods must live as long as the AST they belong to. The Rust borrow checker cannot verify this relationship without the lifetime being declared on the traits themselves.

## Expected Behavior

- The global reference checking trait should be parameterized by a lifetime, so that methods accepting identifier references can express that those references share the same lifetime as the containing AST.
- All existing implementations of these traits (in both library and test code) should be updated to match the new lifetime-parameterized signatures.
- After these changes, the entire minifier crate should compile successfully, and all 328 tests should pass.

## Why This Matters

This is a blocking compilation error. No tests in the minifier crate can run at all until the traits and their implementations are updated to correctly propagate the AST lifetime. Any downstream code that implements these traits also needs to be updated to match the new signatures.
