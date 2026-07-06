I'm working with a Python linter that detects unnecessary dictionary spread operations and offers automatic fixes. The rule works correctly when a dictionary literal is spread directly, but produces broken fixes when the dictionary is wrapped in parentheses before being spread.

For instance, when code spreads a parenthesized dictionary into another dictionary, the linter flags it correctly — but the auto-fix it generates leaves behind orphaned closing parentheses that make the resulting code unparseable. Anyone who applies the fix ends up with a syntax error.

The fix should correctly handle these parenthesized cases by removing the spread operator along with all the enclosing parentheses and the outer dict braces, not just the inner dict braces. It also needs to handle cases where there are multiple layers of parentheses, trailing commas, and comments interspersed between the spread operator and the dictionary content. The comments should be preserved in the output rather than removed. The result must always be syntactically valid Python.

The snapshot file that captures the expected linter output for this rule's test cases also needs to be updated to include the correct expected diagnostics and fix diffs for all new test cases.
