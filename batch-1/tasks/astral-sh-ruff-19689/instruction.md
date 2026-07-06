Implement a mechanism to correctly match workspace diagnostic requests with previous result IDs, even when URIs contain percent-encoded characters. Ensure the server uses decoded file paths for matching instead of raw URI strings to maintain efficient caching and avoid unnecessary re-analysis.

*   Update the server to match previous result IDs by decoded file path:
    *   Decode percent-encoded characters in URIs (e.g., ':' encoded as '%3A') to their original form.
    *   Use the decoded file path to match result IDs instead of the exact URI string.

*   Modify the server's behavior for unchanged files:
    *   Enter long-polling mode if all files are matched and unchanged, suspending the response instead of returning a full diagnostic report.
    *   Ensure that if a shutdown is requested while in long-polling mode, the server returns a workspace diagnostic report with an empty items list.

*   Update the `workspace_diagnostic_request` function:
    *   Accept `work_done_token` as an optional parameter of type `Option<lsp_types::NumberOrString>`.
    *   Accept `previous_result_ids` as an optional parameter of type `Option<Vec<PreviousResultId>>`.
    *   Pass `work_done_token` directly as the `work_done_token` field of `WorkDoneProgressParams`.
    *   Include `previous_result_ids` in the request parameters; use an empty vector if `None`.

*   Modify the `previous_result_ids` field in `ResponseWriter`:
    *   Change the type from `BTreeMap<Url, String>` to `FxHashMap<AnySystemPath, (Url, String)>`.
    *   Use `AnySystemPath` as the key to store the decoded filesystem path.
    *   Store the original URL and result ID string as a tuple in the value.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.