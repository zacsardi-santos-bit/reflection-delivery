Implement a new module for phrase corrections that groups related corrections together in the Harper's grammar linter. Ensure that the module covers a broad set of common writing errors and provides multiple valid alternatives where applicable. Integrate this module into the existing linting system.

*   Create a new module at `harper-core/src/linting/phrase_set_corrections/`.
    *   Implement a `lint_group()` function in `mod.rs` that returns a `LintGroup`.
    *   Declare the module in `harper-core/src/linting/mod.rs` as `mod phrase_set_corrections;`.
    *   Integrate `lint_group()` into the global lint group in `harper-core/src/linting/lint_group.rs`.

*   Implement phrase correction rules in `lint_group()`:
    *   Correct "further adieu" to "further ado" and "much adieu" to "much ado".
    *   Correct "client's side" to "client-side" and "server's side" to "server-side".
    *   Correct "definitive article" to "definite article" and "definitive articles" to "definite articles".
    *   Correct "explanation mark" to "exclamation mark", "explanation marks" to "exclamation marks", and "explanation point" to "exclamation point".
    *   Correct all auxiliary-verb forms of the have/went error (e.g., "have went" to "have gone").
    *   Correct all auxiliary-verb forms of the past/passed error (e.g., "has past" to "has passed").
    *   Correct all inflected forms of "hone in on" to "home in on".
    *   Correct "in details" to "in detail" and "in more details" to "in more detail".
    *   Correct all inflected forms of invest+into to invest+in.
    *   Correct "your point is mute" to "your point is moot".
    *   Correct "operative system" to "operating system" and "operative systems" to "operating systems".
    *   Correct "change tack" variants where tact, tacts, or tacks are incorrectly used.
    *   Correct all inflected forms of "get rid of" where "of" is replaced by "off" or "rid" is replaced by "ride".
    *   Provide two suggestions for "how [pronoun] look[s] like" patterns: remove "like" or replace "how" with "what".
    *   Correct "rise the question" to "raise the question" and "rises the question" to "raises the question".
    *   Provide suggestions for "whole entire" and "a whole entire".
    *   Correct various worse/worst confusion cases.

*   Ensure the corrected forms match the case of the original text.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.