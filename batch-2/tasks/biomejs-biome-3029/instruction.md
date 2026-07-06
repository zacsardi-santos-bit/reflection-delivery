Implement two new reporter options in the Biome CLI to improve integration with CI pipelines. One option should format diagnostics as GitHub Actions workflow annotations, and the other should format them as JUnit XML. Ensure these options work with the check, ci, lint, and format commands, and remove any automatic environment-detection behavior from the default reporter.

*   Update the CLI to accept 'github' and 'junit' as valid values for the --reporter argument.
    *   Ensure these options are supported by the check, ci, lint, and format commands.
*   Implement the GitHub Actions workflow annotation format:
    *   When --reporter=github is used, emit diagnostics as GitHub Actions workflow commands in the format: 
        `::error title=<lint_category>/<lint_rule>,file=<filename>,line=<N>,endLine=<N>,col=<N>,endColumn=<N>::<message>`.
    *   Ensure the 'title' field contains the diagnostic's category path, 'file' contains the filename, and the trailing message is the human-readable diagnostic description.
    *   Do not emit ::error lines for format command issues, as they lack lint rule categories.
*   Implement the JUnit XML format:
    *   When --reporter=junit is used, emit diagnostics as valid JUnit XML with the root element:
        `<testsuites name="Biome" tests="N" failures="N" errors="N" time="...">`.
    *   Count lint rule violations as 'tests' and 'failures', and total diagnostics including formatter errors as 'errors'.
    *   Each lint diagnostic should be represented as a <testsuite> element with a <testcase> child, including a <failure> element with the diagnostic message.
    *   For the format command, output should be `<testsuites name="Biome" tests="0" failures="0" errors="N" time="...">` with no <testsuite> children.
*   Ensure both --reporter=github and --reporter=junit options result in a non-zero exit code when diagnostics are found.
*   Modify the default terminal reporter to stop automatically emitting GitHub Actions workflow command lines when the GITHUB_ACTIONS environment variable is set.
*   Ensure the snapshot infrastructure normalizes the time attribute in JUnit XML output to time="<TIME>" for deterministic snapshots.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.