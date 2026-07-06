## Description

Our staging release process tracks pull requests using a "Staging Deploy Cash" GitHub issue. Currently, each PR in the issue body appears as a multi-line block with two separate checkboxes underneath it — one for QA and one for Accessibility. This makes the issue verbose and harder to read, and it adds unnecessary complexity to the tooling that generates and parses these issue bodies.

We want to simplify the format so each PR is represented by a single checkbox on one line. A PR is either verified (checked) or not — no separate accessibility step is needed.

## Expected Behavior

- Each pull request in the staging deploy cash issue body should appear as a single line: a checkbox followed by the PR URL.
- A PR is marked checked when it has been QA-verified, and unchecked otherwise.
- The functions that generate this issue body should no longer accept or use an "accessible PR list" parameter.
- When checking the issue for deployment blockers, any unchecked checkbox should count as a blocker — there should be no special case that ignores certain items.
- When parsing the issue body back into structured data, PR objects should no longer include an accessibility-verified field.

## Why This Matters

The accessibility check step is redundant in this workflow and complicates both the human-readable issue format and the underlying tooling. Removing it simplifies the release checklist and makes the deploy-blocking logic more straightforward — any outstanding unchecked item is a blocker, full stop.
