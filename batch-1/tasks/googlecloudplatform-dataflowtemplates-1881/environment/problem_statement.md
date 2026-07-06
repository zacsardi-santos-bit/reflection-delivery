## Description

The CI/CD build system supports named module groups that can be used to target builds, tests, and quality checks against a specific set of modules. Right now, only two named groups are supported: a catch-all group and a database migration–specific group. There is no way to run CI pipelines scoped specifically to the Kafka data pipeline modules as a single unit.

We need to add a new named group for Kafka-related modules so that engineers can run spotless checks, checkstyle, unit tests, and integration tests against only the Kafka pipeline code.

## Expected Behavior

- A new named Kafka group should be recognized by the module-selection logic.
- When the Kafka group is selected, it should include the Kafka shared library module, the four Kafka-to-X pipeline modules, and the Maven plugin module.
- When the default mode is used to compute the global exclusion list, shared/common library modules (like the Kafka shared library) should not appear in the exclusion list — only non-common, non-plugin modules from each named group should be negated.

## Why This Matters

Without a dedicated Kafka group, CI pipelines either have to run against all modules (slow and expensive) or list each Kafka module manually. This makes it impossible to have a focused Kafka PR workflow that triggers only on relevant file changes.
