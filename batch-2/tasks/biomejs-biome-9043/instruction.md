I'm using Biome with Tailwind CSS support enabled, and I've run into a parsing issue.

*   When Tailwind directives are enabled in the CSS parser options, parsing a utility at-rule whose name contains a slash character (e.g., `@utility a/b { ... }`) must succeed and produce a valid utility at-rule AST node (TwUtilityAtRule / TW_UTILITY_AT_RULE), with the full slash-containing name treated as a single identifier token.

*   The slash character within a utility name must be lexed as part of the identifier, resulting in a single IDENT token whose text value includes the slash (e.g., the text `a/b` becomes one IDENT token, not two).

*   When Tailwind directives are disabled, parsing a utility at-rule with a slash-containing name must still produce the standard parse diagnostic 'Tailwind-specific syntax is disabled.' at the location of the at-rule, along with the info message 'Enable `tailwindDirectives` in the css parser options, or remove this if you are not using Tailwind CSS.'

*   When Tailwind directives are disabled, the slash-containing utility name must still be lexed as a single IDENT token and the rule must be represented as a bogus (error-recovery) at-rule node (CssBogusAtRule / CSS_BOGUS_AT_RULE).


*   Interface details: NO INTERFACES NEEDED

The tests are snapshot-based parser spec tests. They do not import or call specific functions by name from test code. The tests provide CSS input files and compare the parser's output against expected snapshot files. No new public functions or classes need to be exposed — the fix is entirely internal to the CSS lexer and parser logic.

The snapshots define the required output precisely:

**When Tailwind directives are enabled** (ok case):
- The rule `@utility a/b { color: red; }` must produce a `TwUtilityAtRule` / `TW_UTILITY_AT_RULE` AST/CST node.
- The utility name must be represented as a `CssIdentifier` with a single `IDENT` token whose text is `"a/b"` spanning byte positions 9..13.
- No parse diagnostics should be emitted.

**When Tailwind directives are disabled** (error case):
- The rule `@utility a/b { color: red; }` must produce a `CssBogusAtRule` / `CSS_BOGUS_AT_RULE` AST/CST node.
- The utility name must still be represented as a `CssIdentifier` with a single `IDENT` token whose text is `"a/b"` spanning byte positions 9..13.
- A parse diagnostic must be emitted at line 1, column 2 with the message: `Tailwind-specific syntax is disabled.`
- The diagnostic must include an info note: `Enable \`tailwindDirectives\` in the css parser options, or remove this if you are not using Tailwind CSS.`


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.