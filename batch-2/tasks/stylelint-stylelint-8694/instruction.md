I'd like to add a new stylelint rule that disallows deprecated CSS selectors.

*   The rule must be implemented as a stylelint rule module at lib/rules/selector-no-deprecated/index.mjs with a default export that has ruleName, messages, and meta properties attached.

*   The ruleName must equal the string 'selector-no-deprecated'.

*   The messages object must have a 'rejected' function that returns: Deprecated selector "${selector}" (where ${selector} is the full deprecated selector token including any leading colons), and an 'expected' function that returns: Expected "${unfixed}" to be "${fixed}" (where both arguments include their leading colons).

*   The rule must flag deprecated HTML type selectors (case-insensitive), including at minimum: popup, shadow, acronym, noframes. Deprecated HTML selectors have no auto-fix replacement.

*   The rule must flag deprecated SVG type selectors in a case-SENSITIVE manner. For example, hatchPath (capital P) must be flagged, but hatchpath (all lowercase) must be accepted.

*   The rule must flag deprecated pseudo-classes using case-insensitive matching (e.g., :DROP must be flagged just like :drop). Deprecated pseudo-classes with no replacement include: :drop, :contains, :fullscreen-ancestor. These must be reported as unfixable.

*   The rule must flag deprecated pseudo-classes that have modern replacements and auto-fix them: :focus-ring → :focus-visible, :matches → :is, :-webkit-any → :is, :-moz-any → :is, :top-layer → :open.

*   The rule must flag deprecated pseudo-elements using case-insensitive matching (e.g., ::SHAdOW must be flagged). Deprecated pseudo-elements with no replacement include: ::shadow. These must be reported as unfixable.

*   The rule must flag deprecated pseudo-elements that have modern replacements and auto-fix them: ::content → ::slotted.

*   When auto-fixing, the rule must produce edit info (range and text) that replaces only the name portion of the selector (the part after the colon(s)), not the entire selector token, compatible with computeEditInfo mode.

*   The rule must report precise source positions: column, endColumn, line, endLine for each deprecated selector found.

*   When a CSS rule contains multiple deprecated selectors, the rule must report a separate warning for each deprecated selector with its own position information.

*   The rule must support a secondary option 'ignoreSelectors' which accepts an array of strings and/or regular expression patterns. A deprecated selector (matched by its name without leading colons) that matches any entry in ignoreSelectors must not be reported. String entries are matched literally; RegExp entries use regex matching.

*   Valid/modern selectors must not be flagged: standard HTML elements (abbr, circle), standard pseudo-classes (:hover, :fullscreen, :nth-child), standard pseudo-elements (::first-line, ::backdrop), complex selectors, and at-rules like @font-face.

*   The rule must be registered in lib/rules/index.mjs so it is available under the name 'selector-no-deprecated'.


*   Interface details: Type: Module
Name: selector-no-deprecated
Location: lib/rules/selector-no-deprecated/index.mjs
Description: A stylelint rule module that disallows deprecated CSS selectors. Must be the default export of the module. The exported value must have the following properties attached: `ruleName` (string), `messages` (object), and `meta` (object with `fixable: true`).

Type: String constant
Name: ruleName
Location: lib/rules/selector-no-deprecated/index.mjs
Description: Must equal `'selector-no-deprecated'`. Attached to the default export as `rule.ruleName`.

Type: Messages object
Name: messages
Location: lib/rules/selector-no-deprecated/index.mjs
Description: Attached to the default export as `rule.messages`. Must contain:
- `rejected(selector)` → returns the string `Deprecated selector "${selector}"` where `${selector}` is the full selector token including any leading `:` or `::`.
- `expected(unfixed, fixed)` → returns the string `Expected "${unfixed}" to be "${fixed}"` where `${unfixed}` is the original deprecated selector token (including `:` or `::`) and `${fixed}` is the modern replacement (including `:` or `::`).

Type: Rule function
Name: rule
Location: lib/rules/selector-no-deprecated/index.mjs
Signature: rule(primary, secondaryOptions) → postcss plugin function
Description: The main rule function. Accepts `primary` (boolean `true`) and optional `secondaryOptions` object with an `ignoreSelectors` array of strings and/or RegExp patterns. Returns a PostCSS plugin. Must be the default export.

Secondary options schema:
- `ignoreSelectors`: array of strings or RegExp values. A deprecated selector is skipped if its name (without `:` or `::` prefix) matches any entry. String entries are matched literally; RegExp entries use regex matching.

Deprecated selectors detected (these are the minimum sets the tests require):

Deprecated HTML type selectors (case-insensitive): `acronym`, `popup`, `noframes`, `shadow`, and others in the set
Deprecated SVG type selectors (case-SENSITIVE): `hatchPath` is deprecated; `hatchpath` (lowercase) is NOT deprecated
Deprecated pseudo-classes (case-insensitive detection, stored as lowercase):
  - `:drop` — no replacement (unfixable)
  - `:focus-ring` → `:focus-visible` (fixable)
  - `:matches` → `:is` (fixable)
  - `:-webkit-any` → `:is` (fixable)
  - `:-moz-any` → `:is` (fixable)
  - `:top-layer` → `:open` (fixable)
  - `:contains` — no replacement (unfixable)
  - `:fullscreen-ancestor` — no replacement (unfixable)
Deprecated pseudo-elements (case-insensitive detection):
  - `::shadow` — no replacement (unfixable)
  - `::content` → `::slotted` (fixable)

Fix format: when a deprecated selector has a replacement, the fix must use `range` (character offset range in the CSS source) and `text` (replacement string) compatible with `computeEditInfo: true` in the test framework. The fix replaces only the name portion after `:` or `::`, not the entire rule.

Rule registration: the rule must also be registered in `lib/rules/index.mjs` under the name `'selector-no-deprecated'`.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.