I've found a bug in the Gleam compiler's handling of list patterns and clause guards.

*   When compiling a case clause whose pattern captures the tail of a list (e.g. matching the first element and binding the rest to a variable), and whose guard expression constructs a new list using that same tail variable, the Erlang code generator must include the tail variable in the guard's list construction using Erlang cons-cell syntax rather than omitting it.

*   When compiling the same pattern for the JavaScript target, the compiler must extract the captured tail into a local variable before evaluating the guard, and the guard expression must use list-prepend operations with that local tail variable rather than ignoring the tail.

*   The JavaScript output for a case clause with a list-tail pattern used in a guard must import the list-prepend utility from the runtime module, since it is required to construct the guarded list from the captured tail.

*   At runtime, a function that matches a list with a tail-capturing pattern and evaluates a guard that compares a newly constructed list (using that captured tail) must return the correct branch result: the branch whose guard is satisfied must be taken, and the fallback branch must be taken when the guard fails.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.