Implement a new lint rule in Biome's nursery group to detect unnecessary escape sequences in JavaScript regular expressions. Ensure the rule provides clear diagnostics and context-specific guidance, along with a safe automatic fix to remove the unnecessary escapes.

*   Implement the `NoUselessEscapeInRegex` rule in the file `crates/biome_js_analyze/src/lint/nursery/no_useless_escape_in_regex.rs`.
    *   Ensure it implements the Biome `Rule` trait.
    *   Register it under the `nursery` lint group with the category `lint/nursery/noUselessEscapeInRegex`.
    *   Use the exact diagnostic message: "The character doesn't need to be escaped."
    *   Provide a safe fix labeled: "Unescape the character."

*   Handle context-specific cases with the following informational hints:
    *   For characters that should only be escaped in the middle of a character class or under the `v` flag: "The character should only be escaped if it appears in the middle of the character class or under the `v` flag."
    *   For characters only special outside a character class: "The character should only be escaped if it is outside a character class."
    *   For characters needing escape outside a character class or under the `v` flag: "The character should only be escaped if it is outside a character class or under the `v` flag."
    *   For `^` not as the first character in a class: "The character should only be escaped if it is the first character of the class."
    *   For `\B` in a character class: "The escape sequence only has meaning outside a character class."
    *   For `\k` without `u` or `v` flag: "The escape sequence is only useful if the regular expression is unicode-aware. To be unicode-aware, the `u` or `v` flag should be used."

*   Ensure the rule does not report valid escape sequences:
    *   Such as `\d`, `\B` outside a character class, `\p{...}` with the `u` flag, standard backreferences, and characters escaped in meaningful positions.

*   Correctly handle ES2024 unicode sets mode (`v` flag):
    *   Recognize special meanings for doubled punctuation sequences in character classes.
    *   Treat escaped `.` inside nested character classes and set operations as useless.

*   Modify additional files for integration:
    *   In `crates/biome_js_analyze/src/lint/nursery.rs`, add `pub mod no_useless_escape_in_regex;` and register `no_useless_escape_in_regex::NoUselessEscapeInRegex` in the `declare_lint_group!` macro.
    *   In `crates/biome_js_analyze/src/options.rs`, add a public type alias `NoUselessEscapeInRegex`.
    *   In `crates/biome_diagnostics_categories/src/categories.rs`, register the diagnostic category string `"lint/nursery/noUselessEscapeInRegex"` with the documentation URL `https://biomejs.dev/linter/rules/no-useless-escape-in-regex`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.