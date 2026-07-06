## Description

The concerto-linter package currently has no naming convention rules, so developers have no automated way to detect inconsistent naming in their Concerto model files. This makes it hard to enforce a consistent style across a project — a model with declaration names that start with an uppercase letter, properties using the wrong case, or enum constants that are not all-uppercase will silently pass without any feedback.

## Expected Behavior

- Type declarations (concept, asset, participant, transaction, event, and enum type names) should follow camelCase: names must begin with a lowercase letter. Any declaration whose name begins with an uppercase letter should be flagged as a violation — one violation per offending name.
- Properties (fields) within non-enum type declarations should follow PascalCase: names must begin with an uppercase letter. Correctly named properties should produce no violations.
- Enum constants (the individual values listed inside an enum) should use an all-uppercase style with words separated by underscores. Constants that deviate from this pattern should each produce one violation.
- Models where all names comply with the above conventions should produce zero violations.

## Why This Matters

Without automated naming checks, teams must rely on manual code review to catch inconsistent naming, which is error-prone and time-consuming. Having a linter that reports naming violations lets developers integrate convention checking into their workflow and catch issues early.
