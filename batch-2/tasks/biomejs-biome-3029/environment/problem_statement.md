## Description

Biome currently has inconsistent behavior when running in certain CI environments. If a specific environment variable is detected, it automatically emits annotation-style output lines alongside normal output — even when using the default reporter. This makes snapshot tests flaky because the same command produces different output depending on the environment, requiring the test infrastructure to strip out those extra lines. There is also no way to obtain structured XML output for integration with CI test reporting dashboards.

## Expected Behavior

- A new reporter option that explicitly formats all diagnostics as CI workflow annotations, producing one annotation line per diagnostic with the rule name, file, line, and column information. This should work with all commands (check, ci, lint, format).
- A new reporter option that formats diagnostics as JUnit XML, with each diagnostic represented as a test case failure. The XML output should include aggregate counts (total tests, failures, errors, elapsed time) and per-file grouping. This should work with all commands.
- The default terminal reporter should stop auto-detecting the CI environment and emitting annotation-style lines. Annotation output should only occur when the user explicitly requests it via the new reporter option.

## Why This Matters

Teams integrating Biome into GitHub pull request workflows currently can't reliably get inline annotations — either they get them mixed with normal output unexpectedly, or not at all when running locally. Teams using test reporting dashboards have no way to consume Biome diagnostics in a standard machine-readable format. Explicit opt-in modes for both use cases would make Biome output predictable and composable.
