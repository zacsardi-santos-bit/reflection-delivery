## Description

Author attribution during evaluations is broken in several important scenarios. The current logic does not correctly handle the case where a user is logged into a cloud account — the cloud identity should always win over any locally set override or environment variable, but currently it does not.

Additionally, when an author is specified programmatically (e.g., in a CI pipeline), that value should be passed as a suggestion to the author resolution logic but should still yield to the cloud identity when cloud authentication is active. Currently the two systems are not connected, so programmers have no way to influence the resolved author while still respecting cloud auth.

A third issue affects the import workflow: when importing a historical evaluation that was originally run by someone else, the current code overwrites the original author with the identity of the person performing the import. This is a regression — the imported author should always be preserved, regardless of who is importing.

Finally, when sharing an evaluation result that already has an author recorded, the system unnecessarily prompts for or looks up an email address. It should skip that step when an author is already present.

## Expected Behavior

- When a user is authenticated with a cloud account, their cloud identity takes priority over all other author sources.
- Programmatic author overrides (e.g., set on a test suite) are respected locally but yield to the cloud identity when cloud auth is active.
- The author field in a test suite configuration is used only as a resolution hint and is not persisted directly into the stored eval configuration.
- Imported evaluations preserve their original author, even when the importing user is a different cloud-authenticated identity.
- Sharing an evaluation skips email collection when the eval already has an author set.

## Why This Matters

These bugs cause evaluations to be misattributed in shared or team environments — either overwriting historical records with the wrong author or ignoring the actual authenticated user's identity. Fixing this ensures that the author field reliably reflects who actually ran or owns an evaluation, which is critical for auditability and collaboration.
