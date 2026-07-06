## Description

The distribution manifest output does not indicate whether the release tag was explicitly provided by the user or was automatically inferred by the tool. This makes it impossible for downstream consumers — such as CI pipelines, third-party integrations, or automation scripts — to distinguish between a real release run and a dry-run or preview state.

## Expected Behavior

- The manifest JSON output should include a boolean field that signals whether the announcement tag was implicit (inferred) rather than explicitly specified.
- When the tag is inferred automatically (e.g., during a pull-request preview run where no tag was explicitly passed), this field should be true.
- When the tag was explicitly provided by the user, the field should be false.
- The field should default to `false` so that existing consumers are not broken.

## Why This Matters

Some third-party tools use the dist manifest to determine whether a run is a "real" release or a preview/dry-run. Without an explicit signal in the manifest, those tools have to use fragile heuristics. Adding this field gives consumers a reliable, first-class way to detect implicit-tag scenarios — particularly useful for upload-on-PR-style workflows where an actual release is not happening.
