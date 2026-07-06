I'm working on the Okteto CLI and want to add a manifest validation command so developers can check their configuration files before deploying. Right now there's no way to validate a manifest without actually triggering a deployment, which means typos and schema mistakes get caught too late.

I need the CLI to expose a validate command that accepts a manifest file path and exits cleanly when the file is valid, or returns an error when the file is broken — whether that's because it's empty, has YAML syntax errors, contains invalid values in specific sections, or references configuration keys that don't exist in the schema.

I also need integration test helpers — a configuration struct (with fields for working directory, manifest path, and home directory) and a function that invokes the CLI validate command and surfaces any errors — along with a set of sample manifest fixtures (both valid and invalid examples) so the end-to-end tests can run against real files.

Finally, there is a small typo in an existing schema test: one test case has a misspelling in its label that should be corrected to accurately reflect the sample it references.
