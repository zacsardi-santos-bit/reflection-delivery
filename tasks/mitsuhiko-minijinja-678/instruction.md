Implement the ability to save a reference to a recursive callable within a nested loop in a Jinja-style template engine. Ensure that the recursive callable can be aliased and invoked from within nested loops, and provide a clear error message when the callable is used outside of a loop context.

*   Update the instruction that terminates a for loop frame:
    *   Rename the instruction to `PopLoopFrame`.
    *   Ensure the compiler emits `PopLoopFrame` at the position where the for loop's frame is closed.
    *   Modify the VM to handle `PopLoopFrame` by unwrapping the loop frame and processing any pending recursion jump targets.

*   Implement error handling for recursive callable usage:
    *   When the recursive callable is invoked outside of a recursive for loop context, produce an `UnknownFunction` error.
    *   Ensure the error message is 'loop is unknown'.

*   Allow aliasing of the recursive callable within a recursive for loop:
    *   Enable the use of a set statement (e.g., `{% set parent = loop %}`) to assign the loop callable to another variable.
    *   Ensure the aliased variable remains callable from within nested loops.
    *   Validate that invoking the aliased variable correctly performs recursive rendering on the provided child collection, producing the same output as calling the loop directly.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.