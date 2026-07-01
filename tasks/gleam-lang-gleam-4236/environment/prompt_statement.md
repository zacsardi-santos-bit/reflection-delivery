I'd like to add a new refactoring action to the Gleam language server. When I have a local variable that's assigned a simple value and used exactly once right after, I want the editor to offer an action to "inline" it — removing the binding and substituting the expression directly where the variable is referenced.

The action should work whether my cursor is on the variable at its definition or at the point where it's used. It should apply inside nested blocks and inside case clause branches too, not just in top-level function bodies. When I trigger it, the let binding line should be removed and the value should appear inline at the usage site.

At the same time, the action should not be offered in situations where inlining would be incorrect: if the variable appears more than once (inlining would duplicate the expression), if it was introduced through a destructuring pattern, or if it was bound by a case clause pattern rather than a plain let statement.
