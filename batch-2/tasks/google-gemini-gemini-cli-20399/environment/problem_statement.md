## Description

The CLI currently surfaces all error and diagnostic information unconditionally, which makes the interface feel noisy and alarming even for transient or recoverable situations. Users who just want to get things done are bombarded with error counts in the footer, keyboard hints to open diagnostic panels, failed tool output details, and detailed retry progress — all for errors the system would recover from automatically anyway.

We need a configurable error verbosity setting that lets users opt into a quieter experience. In "low" verbosity mode, recoverable error indicators should be suppressed: the footer should not show error counts or hints to open diagnostic views, failed model-initiated tool outputs should be hidden from the conversation view, retry attempts should show only a generic waiting message rather than detailed attempt counters, and transient capacity failures should be retried automatically without user prompting. When a non-recoverable execution stop does occur in low verbosity mode, the UI should still surface a compact summary indicating that some internal steps failed along with a hint about how to access diagnostics.

In "full" verbosity mode, all existing behavior should be preserved. Debug mode should always behave as full verbosity regardless of the setting.

## Expected Behavior

- A new configurable error verbosity setting is exposed, with "low" as the default and "full" as an alternative
- In low verbosity mode: error summary indicators (count + diagnostic hint) are hidden from the footer
- In low verbosity mode: errored tool calls from the model are hidden from the conversation; client-initiated tool errors remain visible
- In low verbosity mode: retry loading messages show only a generic phrase after the first attempt, not per-attempt counters
- In low verbosity mode: transient quota/capacity errors are retried silently; terminal quota errors still prompt the user
- In low verbosity mode: when execution is stopped by a terminal error, a brief note about suppressed failures and a diagnostic hint are still shown
- Debug mode overrides low verbosity and always shows the full error detail

## Why This Matters

Most users don't need to see every recoverable hiccup the system encounters. A lower-noise default helps users focus on their work while still giving power users and troubleshooters access to full diagnostics when needed.
