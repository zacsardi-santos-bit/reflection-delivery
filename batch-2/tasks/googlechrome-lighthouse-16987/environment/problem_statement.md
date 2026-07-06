## Description

The audit tool that checks for model context protocol support on web pages has a few problems when the feature is unavailable. When the browser does not support the underlying protocol, the gatherer currently throws an unhandled error instead of gracefully reporting that the feature is simply not present. This causes the entire audit run to fail rather than skipping audits that don't apply.

Additionally, there is currently no way to detect whether a page actually exposes the model context interface — the gatherer assumes support as long as the protocol enable command succeeds, but a page may not expose the interface even when the browser supports it.

Finally, when audits pass because nothing relevant was found (no forms, no tools, or all forms already annotated), they currently return a plain passing score without indicating that the audit was skipped. Users can't tell the difference between "everything looks good" and "this audit didn't apply to this page."

## Expected Behavior

- When the browser does not support the model context protocol, the data gatherer should gracefully detect this and report unsupported status instead of throwing an error.
- The gatherer should also check whether the page itself exposes the model context interface and mark the feature as unsupported if it does not.
- The gathered artifact should bundle the support status together with the tool list in a single structured object.
- All audits that depend on model context support should mark themselves as "not applicable" when the feature is unsupported, or when there is simply nothing to audit.

## Why This Matters

Without these changes, running these audits on a browser or page that doesn't support the model context feature fails outright or produces misleading results. Users cannot tell which audits genuinely passed versus which were irrelevant to their page. The fix makes the tool robust in environments where the feature is absent and gives users accurate, actionable feedback.
