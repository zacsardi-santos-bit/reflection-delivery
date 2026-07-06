## Description

Kargo supports updating YAML files as part of promotion workflows, but there is currently no equivalent built-in step for updating JSON files. Teams that store configuration in JSON format — for example, application settings, feature flags, or deployment parameters — have no native way to modify those values during a promotion.

## Expected Behavior

- A new promotion step directive should be available that accepts a path to a JSON file and a list of key/value pairs to update.
- Keys should support dot-notation paths for accessing nested fields (e.g. "app.version" or "features.enabled").
- The step should support multiple value types: strings, numbers, and booleans.
- If the specified file does not exist, the step should fail with an appropriate error.
- The step should produce a commit message output summarizing what was changed, in the same style as other update steps. String values should appear quoted in the message; non-string values should appear unquoted.
- Configuration for the step must be validated: both the file path and the list of updates are required, the path must be non-empty, each update must include a non-empty key and a value.
- When no updates are provided, the step should succeed without modifying the file and without producing a commit message output.
- When an empty JSON file is encountered, it should be treated as an empty object and populated with the specified values.

## Why This Matters

Many modern applications and toolchains use JSON as their primary configuration format. Without this step, teams are forced to work around the limitation using external scripting or tools, rather than expressing the update declaratively within a Kargo promotion workflow.
