Implement a feature in the linter to report unused inline directive comments. Add command-line options to enable this reporting with configurable severity levels. Update the linter's internal logic to track and report these unused directives accurately.

*   Update `LintCommand` struct in `apps/oxlint/src/command/lint.rs`:
    *   Add a public field `inline_config_options: InlineConfigOptions` annotated with `#[bpaf(external)]`.

*   Define `InlineConfigOptions` struct in `apps/oxlint/src/command/lint.rs`:
    *   Include a public field `report_unused_directives: ReportUnusedDirectives`.
    *   Derive `Debug`, `Clone`, and `Bpaf`.

*   Define `ReportUnusedDirectives` enum in `apps/oxlint/src/command/lint.rs`:
    *   Variants:
        *   `WithoutSeverity(bool)` for `--report-unused-disable-directives` flag.
            *   Default: `WithoutSeverity(false)`.
            *   When flag is passed: `WithoutSeverity(true)`.
        *   `WithSeverity(Option<AllowWarnDeny>)` for `--report-unused-disable-directives-severity SEVERITY`.
            *   "warn": `WithSeverity(Some(AllowWarnDeny::Warn))`.
            *   "error": `WithSeverity(Some(AllowWarnDeny::Deny))`.
    *   Derive `Debug`, `Clone`, `PartialEq`, `Eq`, and `Bpaf`.

*   Re-export `ReportUnusedDirectives` in `apps/oxlint/src/command/mod.rs`.

*   Update `DisabledRule` enum in `crates/oxc_linter/src/disable_directives.rs`:
    *   Change to struct variants with `comment_span: Span`:
        *   `All { comment_span: Span }`
        *   `Single { rule_name: &'a str, comment_span: Span }`

*   Update `DisableDirectives` struct in `crates/oxc_linter/src/disable_directives.rs`:
    *   Add fields:
        *   `unused_enable_comments: Box<[(Option<&'a str>, Span)]>`
        *   `used_disable_comments: RefCell<Vec<DisabledRule<'a>>>`
    *   Add methods:
        *   `pub fn unused_enable_comments(&self) -> &[(Option<&'a str>, Span)]`
        *   `pub fn collect_unused_disable_comments(&self) -> Vec<(Option<&'a str>, Span)>`

*   Update `Linter` struct in `crates/oxc_linter/src/lib.rs`:
    *   Add method `pub fn with_report_unused_directives(mut self, report_config: Option<AllowWarnDeny>) -> Self` annotated with `#[must_use]`.

*   Update `LintOptions` struct in `crates/oxc_linter/src/options/mod.rs`:
    *   Add field `pub report_unused_directive: Option<AllowWarnDeny>`.

*   Update `ContextHost` in `crates/oxc_linter/src/context/host.rs`:
    *   Add method `pub fn report_unused_directives(&self, rule_severity: Severity)`.
    *   Ensure it collects and appends diagnostics for unused directives.

*   Ensure that after linting each file, if `report_unused_directive` is set to a warn or deny severity, `ctx_host.report_unused_directives(severity)` is called before `ctx_host.take_diagnostics()`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.