I've been migrating some components from template literal string interpolation to JSX, and I keep accidentally leaving behind stray dollar signs before curly-brace expressions.

*   The rule must be registered in the nursery lint group under the name 'noJsxLeakedDollar' and must be discoverable by the test framework as 'nursery/noJsxLeakedDollar'.

*   The rule must generate a diagnostic whenever a JSX text node ends with a '$' character and its immediately following sibling is a JSX expression child (i.e., content wrapped in curly braces). The diagnostic must be categorized under 'lint/nursery/noJsxLeakedDollar'.

*   The diagnostic message must read exactly: "Possible unintentional '$' before a JSX expression."

*   The diagnostic note must read exactly: "This '$' will be rendered as text. Remove the '$' from the text node or add a suppression if it is intentional."

*   The rule must offer an unsafe fix. The fix action label must read exactly: "Remove dollar sign." The fix must remove only the trailing '$' character from the offending text node, leaving the rest of the text intact.

*   When a JSX element contains multiple '$'-expression pairs (e.g., two separate text-expression occurrences), the rule must emit a separate diagnostic for each offending '$' character.

*   The rule must NOT generate a diagnostic for a text node that contains exactly '$' as its entire content when the parent JSX element has only two children: the text node itself and one JSX expression child. This pattern (e.g., a lone dollar sign before a single expression) is treated as intentional, such as displaying a currency value.

*   The rule must NOT generate a diagnostic for template literals that use '${}' syntax outside of JSX (i.e., regular JavaScript template strings).

*   The rule must NOT generate a diagnostic for a '$' character at the end of a text node when it is NOT followed by a JSX expression child sibling.

*   The rule must NOT generate a diagnostic for JSX expressions that are not preceded by a '$' in the text content.

*   The rule must NOT generate a diagnostic when the dollar sign appears inside a template literal expression that is itself nested within a JSX expression child.


*   Interface details: Type: LintRule
Name: NoJsxLeakedDollar
Location: crates/biome_js_analyze/src/lint/nursery/no_jsx_leaked_dollar.rs
Description: A nursery lint rule that detects JSX text nodes ending with '$' immediately before a JSX expression child. The rule struct must be declared using Biome's `declare_lint_rule!` macro with the name "noJsxLeakedDollar" in the nursery group. It must implement the Rule trait with `type Query = Ast<JsxText>`. The rule must produce a diagnostic with message "Possible unintentional '$' before a JSX expression." and a note reading "This '$' will be rendered as text. Remove the '$' from the text node or add a suppression if it is intentional." The fix kind must be Unsafe, and the fix action label must be "Remove dollar sign."

Type: OptionsStruct
Name: NoJsxLeakedDollarOptions
Location: crates/biome_rule_options/src/no_jsx_leaked_dollar.rs
Description: An empty options struct for the NoJsxLeakedDollar rule. Must be named exactly `NoJsxLeakedDollarOptions` and declared as a public struct with default derivations compatible with Biome's rule options system. The module must be registered as `pub mod no_jsx_leaked_dollar;` in `crates/biome_rule_options/src/lib.rs`.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.