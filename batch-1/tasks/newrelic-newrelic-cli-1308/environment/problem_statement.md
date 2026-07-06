## Description

The recipe installation process currently exposes its main installation entry point only as a private method, making it inaccessible from outside the package and difficult to invoke in integration-level tests. Additionally, once a recipe has been processed, there is no convenient way to query whether a specific recipe reached a particular status — callers must iterate the full statuses list manually. These gaps make the installer harder to use and harder to verify end-to-end.

## Expected Behavior

- The main installation routine should be publicly accessible so external callers and tests can invoke the full installation flow without reaching into internals.
- The installation status tracker should provide a direct query — given a recipe name and a desired status, return whether that recipe has reached that status.
- The installation flow should support an "assume yes" mode where user confirmation prompts are automatically accepted, with that setting propagated into recipe execution context.
- The installer should track each recipe through explicit lifecycle stages (detected, available, installing, installed, failed, recommended, skipped, cancelled, unsupported) and these counts should accurately reflect what happened during a run.
- A data structure representing the result of recipe detection (combining the recipe definition and its detected status) should be available for use in the installation pipeline.

## Why This Matters

Without a public installation entry point and a recipe-status query method, consumers of the installer must work around internal implementation details, making the code brittle and harder to test. Making these interfaces explicit and public improves both usability and testability of the installation system.
