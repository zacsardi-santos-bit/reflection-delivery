I'm hitting two related issues with TypeScript type annotation handling in the parser and in the fast type-stripping tool.

*   The TypeScript parser must parse the `satisfies` keyword with the same operator precedence as `as` (at the relational-operator level). For example, `a + b * c satisfies T / d` must produce an AST equivalent to `(a + b * c satisfies T) / d` — a binary division where the left operand is the entire `TsSatisfiesExpression` node (wrapping `a + b * c` as the expression and `T` as the type annotation) and the right operand is `d`.

*   The TypeScript fast-strip (strip-only) tool must detect when a type assertion — whether `as <Type>`, `as const`, or `satisfies <Type>` — appears in a position where simply removing the type annotation text would change how the surrounding binary operators group at runtime. When this condition is detected, the tool must emit an UnsupportedSyntax error with the exact message: "Type assertions that would change binary expression grouping are not supported in strip-only mode."

*   The precedence-change detection must cover two sub-cases: (1) the next binary operator after the assertion has higher precedence than the operator inside the asserted expression (e.g., `1 + 1 as number / 2` — addition inside, division after); (2) the next binary operator has equal precedence but is not safely associative with the inner operator (e.g., `1 ** 1 as number ** 2` — exponentiation is right-associative, so equal-precedence grouping is not safe).

*   The fast-strip tool must still successfully strip type assertions in the many cases where removal is safe, including: when the type operator is a TypeScript-only operator (bitwise AND or OR in type position), same-operator cases where the operator is safely associative (e.g., `1 * 1 as number * 2`), lower-precedence next operators, equal-tier cross-operator cases where the asserted expression is parenthesized, and cases with intervening comments. In these safe cases the stripped JavaScript output must match the expected result with the type annotation removed.


*   Interface details: NO INTERFACES NEEDED

The tests for this task are entirely data-driven snapshot tests. The parser test discovers `crates/swc_ecma_parser/tests/typescript/satisfies-precedence/input.ts` and compares the parsed AST against `input.ts.json`. The fast-strip tests discover `.ts` input files under `crates/swc_ts_fast_strip/tests/` and compare their outputs against `.swc-stderr` error files or `.js`/`.transform.js` output files. No public function or class names are called by name in the test infrastructure — the implementation changes are internal to `crates/swc_ecma_parser/src/parser/expr.rs` and `crates/swc_ts_fast_strip/src/lib.rs`.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.