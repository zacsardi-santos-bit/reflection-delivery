Update the PLC2801 lint rule to detect unnecessary dunder operator method calls in various expression contexts and ensure auto-fixes are correctly classified and parenthesized. Implement changes in the Rust source file and update the snapshot file to reflect these modifications.

*   Modify the `unnecessary_dunder_call.rs` file to:
    *   Detect unnecessary dunder operator method calls when the method's receiver is prefixed with unary operators, including chained unary operators and binary expressions preceded by unary operators.
    *   Detect violations inside lambda function bodies, conditional expressions, container literals (dict, set, list, tuple), comprehensions, generator expressions, subscript values, starred expressions, and slice elements.
    *   Ensure that when a dunder method has no simple operator equivalent, it is flagged without offering an automatic fix.
    *   Classify all auto-fixes as UNSAFE, including existing and new violations.
    *   Wrap replacement operator expressions in parentheses when required by context, such as when the expression is an operand of a binary or unary operator.
    *   Avoid wrapping replacements in extra parentheses in contexts like call expressions, lambda bodies, conditional expressions, container literals, comprehensions, generator bodies, subscripts, starred expressions, or slices.
    *   For forward operator dunders, wrap binary expression arguments in parentheses; avoid parentheses for simple literals, names, attributes, calls, and other simple expressions.
    *   For reverse operator dunders, avoid parentheses for simple names, attributes, or literals; wrap other argument types in parentheses.
    *   Remove redundant nested parentheses around simple literals or names in the fix.

*   Update the `PLC2801 snapshot` file to:
    *   Reflect all new diagnostics for the newly added fixture cases.
    *   Change the classification from "Safe fix" to "Unsafe fix" for every existing operator-replacement diagnostic.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.