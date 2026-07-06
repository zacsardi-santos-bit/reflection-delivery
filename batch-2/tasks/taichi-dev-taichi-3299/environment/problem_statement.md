## Description

The experimental AST refactorer for Taichi kernels is missing support for several common Python language constructs. Developers who enable the experimental refactorer and write kernels using augmented assignment operators, ternary conditional expressions, or static tuple unpacking will find these constructs either produce incorrect results or fail with unhelpful errors.

Additionally, there are two error-handling gaps: when a developer mistakenly tries to assign a static value to an element of a static array, or re-declares a variable that already exists using a static assignment, the error raised is an opaque assertion failure rather than a descriptive syntax error.

Finally, all kernel constructs are hardcoded to assume the module is imported under a specific conventional alias. If a developer imports the module under a different name, the kernel silently breaks.

## Expected Behavior

- Augmented assignment operators should work correctly inside Taichi kernels when the experimental refactorer is enabled, producing the same result as computing the operation manually
- Ternary conditional expressions (both regular and static) should be supported inside kernels
- Unpacking multiple values from a static call into separate local variables should work
- Attempting static assignment to an array element should raise a descriptive syntax error indicating that this operation is not permitted on array elements
- Attempting to re-declare an existing variable via static assignment should raise a descriptive syntax error indicating that variable re-creation is not permitted
- Kernel constructs should work regardless of what name the module was imported under

## Why This Matters

These are fundamental Python constructs that developers expect to work in Taichi kernels. Without support for these patterns, the experimental AST refactorer is too restrictive for real-world use. The improved error messages also make debugging much easier.
