I'm working on a GitHub Action that runs security scans on pull requests.

*   The function `partitionReviewCommentsByDiff` in `code-scan-action/src/github.ts` must accept a GitHub token string, a pull-request context object, and an array of Comment objects, and must return a Promise resolving to an object with three fields: `lineComments` (comments successfully mapped to a visible diff line, possibly with line numbers clamped to the nearest valid diff line), `generalComments` (comments that have no file or line — e.g. fileless findings), and `invalidLineComments` (comments whose file or line could not be placed anywhere in the visible diff).

*   The function `hasPrPostableFindings` in `src/codeScan/util/github.ts` must accept a Comment array and return a boolean: `true` when at least one comment has a non-empty finding text and a severity value other than NONE (fileless comments with severity HIGH/MEDIUM/LOW are considered PR-postable); `false` when all comments have severity NONE.

*   The function `hasSarifReportableFindings` in `src/codeScan/util/sarif.ts` must accept a ScanResponse object and return a boolean: `true` when at least one comment has a non-null `file` field and a severity value that would not be filtered by `scanResponseToSarif` (i.e. not NONE); file-only findings (file set, line null) are SARIF-reportable; fileless findings (file null) are NOT SARIF-reportable regardless of severity; an empty comments array must return `false`.

*   The `prepareComments` function in `src/codeScan/util/github.ts` must now route file-only comments (file field set, line null) into `generalComments` rather than ignoring them. Both fileless comments (null file) and file-only comments (non-null file, null line) with severity other than NONE must appear in the `generalComments` result array.

*   When the scan response includes a `skipReason` but there are no SARIF-reportable findings and no PR-postable findings (e.g. all findings have severity NONE), the action must log the skip via an info message in the format `🔀 Scan skipped: <skipReason>` and return without writing SARIF or posting any PR comments.

*   When the scan response includes a `skipReason` alongside at least one SARIF-reportable or PR-postable finding, the action must emit a warning with the exact message: `Scan response included findings alongside a skipReason ("<skipReason>"); processing findings.` and then proceed to process and surface those findings.

*   SARIF output must only be written when `hasSarifReportableFindings` returns true for the scan response. A mixed-skip response containing only fileless findings (no file field) must not trigger a SARIF write, and the `sarif-path` output must not be set.

*   General PR comments for findings that include both a file and a line number must format their body with the location as a bold prefix in the form `**file:line**` followed by the finding text. General PR comments for file-only findings (file set, line null) must use `**file**` as the bold prefix. General PR comments for fileless findings must include the finding text directly without a location prefix.

*   When fallback comment posting is invoked, line comments that cannot be placed in the PR diff (returned as `invalidLineComments` by `partitionReviewCommentsByDiff`) must be merged with the `generalComments` and posted as general PR issue comments with appropriate location prefixes.


*   Interface details: Type: Function
Name: partitionReviewCommentsByDiff
Location: code-scan-action/src/github.ts
Signature: partitionReviewCommentsByDiff(token: string, context: PullRequestContext, comments: Comment[]) -> Promise<{ lineComments: Comment[]; generalComments: Comment[]; invalidLineComments: Comment[] }>
Description: Validates review-comment locations against the current PR diff. Returns three arrays: `lineComments` contains comments that were successfully mapped to a line visible in the diff (line numbers may be clamped); `generalComments` contains comments that have no file or line (fileless); `invalidLineComments` contains comments whose file or line could not be placed in the diff. Replaces the previous `postReviewComments` export.

Type: Function
Name: hasPrPostableFindings
Location: src/codeScan/util/github.ts
Signature: hasPrPostableFindings(comments: Comment[]) -> boolean
Description: Returns true when at least one comment has a non-empty finding and a severity value other than NONE. Fileless comments (file: null, line: null) with a real severity count as PR-postable. Returns false when the array is empty or all comments have severity NONE.

Type: Function
Name: hasSarifReportableFindings
Location: src/codeScan/util/sarif.ts
Signature: hasSarifReportableFindings(response: ScanResponse) -> boolean
Description: Returns true when the scan response contains at least one comment that would produce a result in the SARIF output — i.e., the comment has a non-null `file` field and a severity that is not NONE. File-only findings (file set, line null) are SARIF-reportable. Fileless findings (file null) are NOT SARIF-reportable even if severity is high. Returns false for an empty comments array or when every comment would be filtered by the SARIF serializer.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.