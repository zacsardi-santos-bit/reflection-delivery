Implement a new summary reporting mode for the CLI tool to provide a high-level overview of diagnostics. This mode should aggregate and summarize issues by category, offering a concise view of codebase health across all files. Ensure the summary mode works with all major commands and update the help text to reflect this new option.

*   Accept 'summary' as a valid value for the `--reporter` flag.
    *   Update the help text for all commands (check, ci, format, lint, migrate, rage) to display the reporter option as `--reporter=<json|json-pretty|summary>`.
*   Implement the summary output for the `check` and `ci` commands:
    *   Include a 'Formatter' section with the header 'Formatter', a separator line, the message 'The following files needs to be formatted:', and a list of affected file names.
    *   Include an 'Organize Imports' section with the header 'Organize Imports', a separator line, the message 'The following files needs to have their imports sorted:', and a list of affected file names.
    *   Include an 'Analyzer' section with the header 'Analyzer', a separator line, the message 'Some analyzer rules were triggered', and a table with 'Rule Name' and 'Diagnostics' columns.
        *   Order lint rules by rule name length, displaying the longest first.
*   Implement the summary output for the `format` command:
    *   Display only the 'Formatter' section.
    *   Return an error if formatter issues are found and include a summary footer reporting the number of formatter errors.
*   Implement the summary output for the `lint` command:
    *   Display only the 'Analyzer' section.
    *   Return an error if lint issues are found.
*   Ensure the `check` and `ci` commands display all three sections ('Formatter', 'Organize Imports', 'Analyzer') and return an error if issues are found.
*   Conclude the summary output with a footer line reporting the number of files checked and total errors found, formatted as 'Checked N files in <TIME>. No fixes needed. Found N errors.'
*   Develop the 'summary' reporter as a module that handles diagnostic grouping and the summary footer, integrating it into the existing reporter selection mechanism.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.