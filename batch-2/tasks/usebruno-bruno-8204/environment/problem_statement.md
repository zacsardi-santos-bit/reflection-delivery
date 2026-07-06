# OpenAPI Sync: Preserve User Customizations When Merging Spec Updates

## Description

When users sync their Bruno requests against an updated OpenAPI specification, all of their customizations get lost. Parameter values they filled in, authentication credentials, environment variable placeholders embedded in request bodies, and test scripts are all wiped out and replaced with the spec's blank template values.

Users want to be able to pull in structural changes from an updated spec (new parameters, removed fields, URL changes, auth mode changes) while keeping the values they've already entered. Right now that's simply not possible — it's an all-or-nothing overwrite.

## Expected Behavior

- When a parameter or header also exists in the updated spec, the user's current value should be kept
- New parameters or headers introduced by the spec should be added with the spec's default value
- Parameters or headers removed from the spec should be dropped
- JSON request bodies should be merged field-by-field: user values are preserved for existing fields, new spec fields are added, removed spec fields are dropped
- Template variable references embedded in request bodies (e.g. environment variables) must survive the merge intact and the resulting body must remain structurally valid
- Authentication configuration should be preserved when the auth type hasn't changed; if the spec changes the auth type, the spec wins
- Custom test scripts, pre-request scripts, and assertions attached to a request are never touched by the sync
- A full-reset option should be available to completely overwrite all request values from the spec (still keeping scripts/tests/assertions)
- A way to compare what has actually changed between a spec version and the current request should be available, so the UI can show a diff to the user

## Why This Matters

Users invest time customizing requests with real values and environment variable references. Losing all of that on every spec re-sync is a serious workflow blocker. Intelligent merging makes spec updates practical for teams that maintain living API specifications.
