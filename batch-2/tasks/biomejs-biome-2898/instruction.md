Introduce a "fix" flag to the CLI tool as an alias for the existing "write" behavior across all relevant subcommands (check, format, lint, and migrate). Implement a separate "unsafe" flag for the check and lint subcommands to allow users to opt into riskier automatic fixes. Ensure backward compatibility with existing flags and handle any incompatible flag combinations with clear error messages.

*   Implement the "fix" flag:
    *   Ensure the check command accepts a --fix flag that behaves identically to --write.
    *   Ensure the format command accepts a --fix flag that behaves identically to --write.
    *   Ensure the lint command accepts a --fix flag that behaves identically to --write.
    *   Ensure the migrate command accepts a --fix flag that behaves identically to --write.

*   Implement the "unsafe" flag:
    *   Ensure the check command accepts an --unsafe flag to apply unsafe fixes when combined with --fix or --write.
    *   Ensure the lint command accepts an --unsafe flag to apply unsafe fixes when combined with --fix or --write.

*   Maintain backward compatibility:
    *   Retain --apply as an alias for --write and --apply-unsafe as an alias for --write --unsafe for check and lint commands.

*   Handle incompatible flag combinations:
    *   Fail with an error when check is invoked with both --fix and --apply, or --write and --apply.
    *   Fail with an error when lint is invoked with both --fix and --write.
    *   Fail with an error when migrate is invoked with both --fix and --write.

*   Implement error handling for auto-fixing:
    *   When auto-fixing is requested but some issues remain unfixable, apply all available fixes, write changes, and return a non-zero exit code with an appropriate message.

*   Update help output:
    *   Update help for check, lint, format, and migrate commands to include new flag descriptions and usage lines.

*   Define and implement the following in `crates/biome_cli/src/commands/mod.rs`:
    *   `FixFileModeOptions` struct with private boolean fields: apply, apply_unsafe, write, fix, unsafe_.
    *   `determine_fix_file_mode` function to determine the fix file mode from given options.
    *   `check_fix_incompatible_arguments` function to validate flag combinations.

*   Update `FixFileMode` in `crates/biome_service/src/workspace.rs` to derive `PartialEq`.

*   Implement unit tests in a `#[cfg(test)] mod tests` block inside `crates/biome_cli/src/commands/mod.rs`:
    *   Test cases: incompatible_arguments, safe_fixes, safe_and_unsafe_fixes, no_fix, check_options.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.