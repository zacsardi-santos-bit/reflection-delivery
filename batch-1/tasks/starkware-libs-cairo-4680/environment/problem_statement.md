## Description

While loops are currently parsed and type-checked by the compiler but cannot be compiled any further. When a program contains a while loop, the lowering phase emits an error saying while loops are not yet supported and refuses to produce output. This means any Cairo program that uses a while loop fails to compile, even if the loop is semantically valid.

## Expected Behavior

- While loops should compile end-to-end without errors, producing correct intermediate representation.
- The generated code for a while loop should evaluate the condition on each iteration and branch: if true, execute the body and repeat; if false, exit the loop.
- Variable usage analysis should correctly track which variables are read, written, or introduced in a while loop, considering both the condition expression and the loop body.
- Usage reports should correctly identify the kind of each block-like construct — distinguishing between plain blocks, infinite loops, and while loops — when displaying usage information.

## Why This Matters

While loops are a fundamental control-flow construct. Without lowering support, Cairo developers cannot write programs that iterate until a condition is no longer true, even though the syntax is accepted by the parser and type checker. Implementing this support brings while loops to parity with the existing loop construct and allows programs that rely on condition-based iteration to compile and run correctly.
