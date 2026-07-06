Implement a language server code action titled "Collapse nested case" that merges nested case expressions in Gleam code into a single flat case expression. Ensure that the action is offered when the cursor is on a variable in an outer case clause that matches a nested case expression on the same variable.

*   Register a language server code action with the title "Collapse nested case".
*   Detect when the cursor is on a variable in an outer case clause whose body is a nested case expression on the same variable.
*   Merge the nested case expressions into a single flat case expression:
    *   Substitute the inner pattern for the matched variable in the outer pattern.
    *   Remove block wrappers around inner case expressions.
    *   Preserve all other bound variables from the outer pattern.
    *   Maintain labeled fields in constructor patterns.
    *   Distribute alternative patterns from inner clauses across the outer pattern.
    *   Add `as variable` aliases when the matched variable is used in the inner clause body.
*   Handle guard expressions:
    *   Propagate outer clause guards to every collapsed inner clause.
    *   Preserve inner clause guards in the collapsed output.
    *   Combine guards from both levels with `&&` when both are present.
    *   Wrap guards containing `||` in `{ ... }` before combining with `&&`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.