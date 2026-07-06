## Description

The component macro code generator produces async host function bindings where each generated closure directly returns a boxed async future as its body. This style can cause problems in certain Rust compiler configurations — in particular, it can confuse the borrow checker when the future captures references that span an await point, since without an explicit block the compiler may not delimit the async region properly.

The fix is to update the code generator so that generated async closures always wrap their body in an explicit block expression. The boxed async future should be the last expression inside that block rather than being the naked return value of the closure.

## Expected Behavior

- All generated async function wrappers (for any WIT interface type — integers, floats, chars, strings, lists, flags, records, variants, resources, and more) should enclose their closure body in a block expression.
- The block should contain the inner async future as its sole expression.
- This applies equally to zero-parameter closures and to closures that accept one or more typed arguments.

## Why This Matters

Without this change, the generated bindings may fail to compile under stricter lifetime/borrow analysis or in configurations that expose subtle type-inference differences between block-expression and expression-body closures. Updating the generator to always emit the block form makes the generated code more robust across all Rust compiler versions and host-function configurations.
