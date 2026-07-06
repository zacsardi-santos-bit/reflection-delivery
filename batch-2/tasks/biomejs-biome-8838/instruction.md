I'd like to add a new lint rule to Biome's nursery group that catches a dangerous pattern in JavaScript: passing strings to timer scheduling functions instead of actual callback functions.

*   The lint rule must be named 'noImpliedEval' and must belong to the 'nursery' lint group, reported under the category 'lint/nursery/noImpliedEval'.

*   The rule must report a diagnostic with the error message 'Implied eval() is not allowed.' when a string expression is passed as the first argument to setTimeout, setInterval, or setImmediate.

*   The diagnostic must include an informational note: 'Passing strings to functions like setTimeout, setInterval, or setImmediate is a form of implied eval() and can lead to security and performance issues.'

*   The diagnostic must include a second informational note: 'Use a function instead of a string.'

*   The rule must flag direct calls to setTimeout, setInterval, and setImmediate when the first argument is a string literal, a template literal with no substitutions, or a string concatenation expression (binary + expression where at least one operand is a string).

*   The rule must flag these functions when accessed through the global objects 'window' or 'globalThis' via dot notation (e.g., window.setTimeout), computed member access (e.g., window['setTimeout']), optional chaining (e.g., window?.setTimeout, window?.['setTimeout']), and chained global access (e.g., window.window.setTimeout, globalThis.globalThis.setInterval).

*   The rule must flag string arguments that are wrapped in one or more layers of parentheses (e.g., setTimeout(('code'), 100) or setTimeout((('code')), 100)).

*   The rule must NOT flag these functions when the first argument is a function expression, an arrow function, a non-string value (number, null, undefined, or a variable reference to a non-string), or a template literal containing at least one substitution (e.g., `${variable}`).

*   The rule must NOT flag calls when the function name (setTimeout, setInterval, setImmediate) is shadowed by a local parameter or variable in the enclosing scope.

*   The rule must NOT flag method calls on non-global objects (e.g., obj.setTimeout or someObject['setInterval']), calls using .call() or .apply() style, or member chains that go deeper than one level on a global (e.g., window.foo.setTimeout).

*   The rule must work correctly in both JavaScript (.js) and JSX (.jsx) files, including within component bodies, callback functions, and event handlers.

*   The rule must be implemented in the Rust source file at 'crates/biome_js_analyze/src/lint/nursery/no_implied_eval.rs' and registered in the nursery lint rules module so the spec tests can discover it.


*   Interface details: Type: Rust Lint Rule
Name: NoImpliedEval
Location: crates/biome_js_analyze/src/lint/nursery/no_implied_eval.rs
Description: A lint rule that detects implied eval() usage by flagging string arguments passed to setTimeout, setInterval, and setImmediate. Must be declared using the declare_lint_rule! macro with the rule name "noImpliedEval" in the "nursery" group and registered in the nursery lint module. The rule must produce diagnostics with the exact messages: error "Implied eval() is not allowed.", note "Passing strings to functions like setTimeout, setInterval, or setImmediate is a form of implied eval() and can lead to security and performance issues.", and note "Use a function instead of a string."

Type: Rust Struct (Rule Options)
Name: NoImpliedEvalOptions
Location: crates/biome_rule_options/src/no_implied_eval.rs
Description: An empty options struct for the NoImpliedEval rule. Must be a public struct deriving Default, Clone, Debug, Deserialize, Deserializable, Merge, Eq, PartialEq, Serialize, and optionally JsonSchema. The module must be exported from crates/biome_rule_options/src/lib.rs.

Type: Rule Registration
Name: noImpliedEval (nursery group)
Location: crates/biome_js_analyze/src/lint/nursery/ (mod.rs or equivalent)
Description: The NoImpliedEval rule must be registered in the nursery module so that the spec test framework can discover and run it. The test infrastructure auto-discovers rules by matching the directory name "noImpliedEval" in crates/biome_js_analyze/tests/specs/nursery/ to the registered rule name.

Type: Diagnostic Category
Name: lint/nursery/noImpliedEval
Location: crates/biome_diagnostics_categories/src/categories.rs
Description: The diagnostic category "lint/nursery/noImpliedEval" must be registered with URL "https://biomejs.dev/linter/rules/no-implied-eval" so the rule can emit diagnostics under the correct category.

Type: Configuration Entry
Name: NoImpliedEval (RuleName enum variant)
Location: crates/biome_configuration/src/analyzer/linter/rules.rs
Description: The rule must be registered in the configuration system with the string name "noImpliedEval" and assigned to RuleGroup::Nursery so it can be enabled/disabled via Biome configuration.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.