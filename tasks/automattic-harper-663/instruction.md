Implement a new phrase correction rule in the linter to detect and correct the misuse of "mute point" to "moot point". Ensure the rule is consistent with existing phrase correction rules in the codebase.

*   Define a new struct named `MutePoint` in `harper-core/src/linting/phrase_corrections.rs`.
    *   Ensure `MutePoint` is publicly exported from the `phrase_corrections` module.
*   Implement the `Default` trait for `MutePoint` to allow instantiation via `MutePoint::default()`.
    *   Signature: `MutePoint::default() -> MutePoint`
*   Ensure that when the `MutePoint` rule is applied:
    *   It detects the phrase "mute point" in the text.
    *   It produces a lint suggestion to replace "mute point" with "moot point".

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.