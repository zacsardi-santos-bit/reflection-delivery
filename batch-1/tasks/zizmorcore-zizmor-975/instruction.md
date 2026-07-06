Enhance the GitHub Actions security linter output to indicate which findings are automatically fixable. Annotate each fixable finding with a note and update the summary line to include the count of fixable findings.

*   Include a note in the plain-text diagnostic output for each finding with an available fix:
    *   Append the exact text 'this finding has an auto-fix' after the audit confidence annotation in the finding's footer.
*   Update the overall findings summary line to reflect the count of fixable findings:
    *   Use the format 'N findings (K fixable): ...' when only fixable findings are present.
    *   Use the format 'N findings (M suppressed, K fixable): ...' when both suppressed and fixable findings are present.
*   Ensure specific rules are treated as fixable:
    *   The credential-persistence rule (artipacked) must be reported as fixable.
    *   The template-injection rule must be reported as fixable.
*   Do not annotate or count as fixable findings from rules without associated fixes:
    *   The unpinned-uses rule must not display the auto-fix note or be included in the fixable count.
*   Count a finding as fixable only if all associated fixes reference local inputs.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.