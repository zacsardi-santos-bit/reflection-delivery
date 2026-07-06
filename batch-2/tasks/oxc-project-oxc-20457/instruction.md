I'm seeing a false positive from the unused-variables lint rule in my project.

*   When a variable is reassigned by calling a method on its own current value (a self-assignment via method call) and this assignment occurs inside the body of a loop (for, for-in, for-of, while, or do-while), the no-unused-vars rule must NOT report that variable as unused.

*   When a variable is reassigned by calling a method on its own current value but this assignment does NOT occur inside any loop body, the no-unused-vars rule must still flag the variable as assigned but never used.

*   The loop-body detection must stop at function (including arrow function) and program boundaries — a self-assignment inside a callback or inner function nested inside a loop should not be treated as if it is in the loop body.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.