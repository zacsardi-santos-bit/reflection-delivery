I'm hitting a parser bug when using Svelte list-rendering blocks with a method call or function call as the key expression.

*   The Svelte HTML parser must parse a list-rendering block whose key expression contains a method call (parentheses within the outer key parentheses, e.g., a call like `number.toString()`) without producing any parse errors.

*   The Svelte HTML parser must parse a list-rendering block whose key expression contains a function call with an argument (e.g., `key(number)`) without producing any parse errors.

*   When the key expression contains nested parentheses, the full expression text — including the inner parentheses — must be captured as the content of the key node; the outer parentheses delimit the key and must not be consumed by the inner call.

*   The test input file must be placed at `crates/biome_html_parser/tests/html_specs/ok/svelte/each_with_method_call_key.svelte` with exactly the two list-rendering blocks shown (one using `number.toString()` as the key with an index variable, one using `key(number)` as the key without an index variable).

*   The corresponding snapshot file at `crates/biome_html_parser/tests/html_specs/ok/svelte/each_with_method_call_key.svelte.snap` must record the expected AST and CST, with the key expression content stored as an `HtmlTextExpression` whose literal token captures the full call text (e.g., `"number.toString()"` and `"key(number)"` respectively) and the outer parentheses represented as separate `L_PAREN` and `R_PAREN` tokens in a `SvelteEachKey` node.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.