Implement two new lint rules to restrict CSS selector relationship types in stylesheets: a blacklist rule to forbid specified combinators and a whitelist rule to allow only specified combinators. Additionally, create a utility function to identify standard CSS combinators.

*   Implement `isStandardSyntaxCombinator` in `lib/utils/isStandardSyntaxCombinator.js`.
    *   Accept a combinator node from postcss-selector-parser.
    *   Return true for standard CSS combinators: descendant space, tab, newline, `>>`, `>`, `+`, `~`.
    *   Return false for reference combinators of the form `/word/` (any casing).

*   Implement `selector-combinator-blacklist` rule in `lib/rules/selector-combinator-blacklist/index.js`.
    *   Export `ruleName` as `"selector-combinator-blacklist"`.
    *   Export a `messages` object with a `messages.rejected(combinator)` method.
    *   Accept an array of forbidden combinator strings as the primary option.
    *   Report a violation with `messages.rejected(combinator)` if a forbidden combinator is used.
    *   Normalize all whitespace descendant combinator variants to a single space `" "` for comparison.
    *   Ignore non-standard reference combinators such as `/for/`, `/FOR/`, `/fOr/`.
    *   Register the rule in `lib/rules/index.js` under the key `"selector-combinator-blacklist"`.

*   Implement `selector-combinator-whitelist` rule in `lib/rules/selector-combinator-whitelist/index.js`.
    *   Export `ruleName` as `"selector-combinator-whitelist"`.
    *   Export a `messages` object with a `messages.rejected(combinator)` method.
    *   Accept an array of allowed combinator strings as the primary option.
    *   Report a violation with `messages.rejected(combinator)` if a non-allowed combinator is used.
    *   Normalize all whitespace descendant combinator variants to a single space `" "` for comparison.
    *   Ignore non-standard reference combinators such as `/for/`, `/FOR/`, `/fOr/`.
    *   Register the rule in `lib/rules/index.js` under the key `"selector-combinator-whitelist"`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.