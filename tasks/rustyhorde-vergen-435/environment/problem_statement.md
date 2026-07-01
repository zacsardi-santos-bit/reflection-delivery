## Description

When the build tool cannot retrieve git information (for example, because it is running outside of a git repository), it currently silently fills all git-related build variables with a static placeholder value. This happens even in the default, non-idempotent mode, which is confusing: the build appears to succeed with fake values even when git context is genuinely unavailable.

The desired behavior is to distinguish between the two modes clearly:

- In the default (non-idempotent) mode, when git information cannot be retrieved, the tool should emit diagnostic warning messages for each variable it could not set, rather than inserting placeholder defaults into the build environment.
- In idempotent mode (explicitly opted into by the caller), the existing behavior should be preserved — placeholder defaults should still be used and announced via warnings.

## Expected Behavior

- Default mode (no idempotent flag): emits a warning message indicating it was unable to set each git variable it cannot populate; no default value is inserted into the build environment.
- Idempotent mode (opt-in): emits a warning message indicating each git variable has been set to a default value for each variable it cannot populate, and inserts the placeholder default value as before.

## Why This Matters

Developers building outside of a git repository now get clear diagnostic feedback about which variables could not be set, rather than silently receiving placeholder values that may not reflect the actual build state. Idempotent builds (used in CI reproducibility scenarios) retain their existing behavior and must be explicitly enabled by the caller.
