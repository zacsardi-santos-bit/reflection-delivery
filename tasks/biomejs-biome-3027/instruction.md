Implement a new GitLab reporter option for your CLI tool that outputs diagnostics in a format compatible with GitLab's Code Quality feature. Ensure that the output is a JSON array with specific fields for each diagnostic finding and update the CLI help text to reflect the new option. Additionally, correct the formatting of certain diagnostic messages to ensure consistency across outputs.

*   Update the CLI to accept 'gitlab' as a valid value for the `--reporter` flag.
    *   Commands affected: check, ci, format, lint, migrate, rage.
    *   Update help text to list reporter options as: `<json|json-pretty|github|junit|summary|gitlab>`.

*   Implement the GitLab reporter functionality:
    *   When `--reporter=gitlab` is used with check, ci, or lint commands:
        *   Emit a JSON array to the console output.
        *   Each JSON object must include:
            *   'description': Single-line diagnostic message.
            *   'check_name': Rule identifier (e.g., lint/suspicious/noDoubleEquals).
            *   'fingerprint': Unique string from hashing (see fingerprint algorithm).
            *   'severity': Mapped from internal severity levels (see severity mapping).
            *   'location': Object with:
                *   'path': Relative file path.
                *   'lines': Object with 'begin' field (1-based line number).
    *   When `--reporter=gitlab` is used with the format command:
        *   Emit an empty JSON array `[]`.
    *   Return an error result if issues are found with `--reporter=gitlab`.

*   Correct diagnostic message formatting:
    *   Ensure the double-equals lint rule message is single-line: 'Use === instead of ==. == is only allowed when comparing against `null`'.
    *   Apply this format in JUnit XML and LSP diagnostic messages.

*   Implement the GitLab reporter module:
    *   Create `gitlab.rs` in `crates/biome_cli/src/reporter/`.
    *   Register as `pub(crate) mod gitlab` in `crates/biome_cli/src/reporter/mod.rs`.
    *   Define `GitLabReporter` struct with `execution` and `diagnostics` fields.
    *   Implement `write` method to delegate to `report_diagnostics`.

*   Implement `GitLabReporterVisitor`:
    *   Define in `gitlab.rs` with `new` constructor.
    *   Implement `report_diagnostics` to format and log GitLab JSON.
    *   `report_summary` should be a no-op.

*   Update enum variants:
    *   Add `CliReporter::GitLab` in `crates/biome_cli/src/cli_options.rs`.
    *   Add `ReportMode::GitLab` in `crates/biome_cli/src/execute/mod.rs`.

*   Implement fingerprint algorithm:
    *   Use Rust's `std::hash::DefaultHasher` for initial hash.
    *   Ensure uniqueness by rehashing if necessary.
    *   Serialize final u64 as a decimal string.

*   Map severity levels:
    *   Hint -> "info"
    *   Information -> "minor"
    *   Warning -> "major"
    *   Error -> "critical"
    *   Fatal -> "blocker"

*   Filter diagnostics:
    *   Include only those with severity >= configured level.
    *   Include only diagnostics with both span and source_code.

*   Relativize file paths:
    *   Use `path-absolutize` crate for path handling.
    *   Ensure paths are relative to the working directory.

*   Add dependencies:
    *   `path-absolutize` (version "3.1.1", feature "use_unix_paths_on_wasm").
    *   `serde` crate with derive feature for JSON serialization.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.