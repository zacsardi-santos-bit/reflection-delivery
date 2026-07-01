Implement support for command aliases in the Telegram bot using the teloxide framework. Allow multiple names for the same command, including localized versions, and provide options to hide aliases from the help message while keeping them functional.

*   Update the `BotCommands` derive macro to support:
    *   `#[command(alias = "string")]` attribute on enum variants for single alias declaration.
    *   `#[command(aliases = ["a", "b", ...])]` attribute on enum variants for multiple alias declarations.
    *   `#[command(hide_aliases)]` attribute to hide aliases from the help message while keeping them parseable.
*   Ensure aliases can contain non-ASCII characters for localization.
*   Modify the `CommandDescription` struct in `crates/teloxide/src/utils/command.rs`:
    *   Add a field `aliases: &'a [&'a str]` to store alias strings (without prefix).
    *   Use `&[]` for commands without aliases.
*   Ensure the `descriptions().to_string()` output:
    *   Formats commands with aliases as `/command, /alias1, /alias2 — description`.
    *   Remains `/command — description` for commands without aliases.
*   Ensure `hide_aliases` attribute:
    *   Hides aliases from the help message but keeps them parseable.
    *   Has no effect when applied to commands without aliases.
*   Allow combining `hide` and `alias`/`aliases` attributes on the same variant.
*   Restrict `alias`, `aliases`, and `hide_aliases` attributes to enum variants only:
    *   Applying them to the enum itself should produce a compile error.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.