Simplify the staging release management tooling by updating the format of pull request entries in the "Staging Deploy Cash" GitHub issue. Implement a single checkbox per pull request to indicate QA verification, removing the accessibility tracking step.

*   Update the `generateStagingDeployCashBody` function:
    *   Remove the `accessiblePRList` parameter, reducing the total parameters to six: `tag`, `PRList`, `verifiedPRList`, `deployBlockers`, `resolvedDeployBlockers`, `resolvedInternalQAPRs`.
    *   Ensure the PR list section header ends with a CRLF line break (`\r\n`) immediately before the first PR entry, with no blank line in between.
    *   Format each PR entry as a single line: `'- [x] URL\r\n'` for verified PRs or `'- [ ] URL\r\n'` for unverified PRs.
    *   Separate PR entries with single CRLF line breaks (`\r\n`), and ensure two additional CRLF sequences (`\r\n\r\n`) follow the final PR entry before the next section.

*   Modify the `getStagingDeployCashPRList` function:
    *   Parse the PR list section using the new single-line checkbox format (`"- [ ] URL"` or `"- [x] URL"`).
    *   Return PR objects containing exactly `url` (String), `number` (Number), and `isVerified` (Boolean).
    *   Exclude the `isAccessible` field from the returned PR objects.

*   Adjust the deployment blocker check logic:
    *   Treat any unchecked checkbox pattern (`'- [ ] '`) as a deployment blocker.
    *   Remove any special exceptions or negative lookaheads that previously ignored items labeled 'Accessibility'.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.