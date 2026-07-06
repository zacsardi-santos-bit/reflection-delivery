## Description

When writing test suites, it's easy to accidentally give two different test cases or two different test group blocks the same title, especially as a file grows over time. This makes debugging painful: when a test fails, you can't determine which one actually failed just from its title. Other popular testing linters already flag this pattern, but Biome currently has no equivalent rule.

## Expected Behavior

A new lint rule in the nursery group should detect when two tests or two test grouping blocks share the same title at the same nesting level. Specifically:

- Two individual test cases with the same title in the same scope should produce a warning
- Two test grouping blocks with the same title at the same level should produce a warning
- The warning should tell the developer that a duplicate title already exists in the same scope and suggest renaming it
- The rule should only compare titles that can be determined statically — dynamic titles using interpolated expressions should be ignored
- Scoping should be respected: the same title in different nested blocks is perfectly fine, since they belong to different scopes

## Why This Matters

Duplicate test titles make test output ambiguous and reduce the value of descriptive test naming. Catching this at lint time, before running tests, saves developers from confusing failure reports. This rule also enables migration from equivalent rules available in popular JavaScript testing lint plugins.
