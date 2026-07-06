## Description

When viewing experiment evaluations, runs that cannot produce a pass/fail result currently show no status at all — the status is simply absent. This happens in two distinct situations: when no experiment item is associated with the run, and when an item exists but no assertions have been defined for it. In both cases, the UI offers no explanation, leaving users confused about why the evaluation shows nothing.

## Expected Behavior

- When a run has no associated experiment item, it should display a clear skipped status with a message explaining that no experiment item is defined, so the user knows they need to create one.
- When a run has an experiment item but no assertions are configured, it should display a skipped status with a message explaining that no assertions are defined, so the user knows to add assertions.

## Why This Matters

Without this distinction, users cannot tell whether an evaluation is genuinely pending, broken, or simply unconfigured. Surfacing a human-readable skip reason for each case dramatically reduces confusion and helps users take the right corrective action.
