## Description

Apache Pinot currently has no automated enforcement to prevent developers from specifying dependency versions directly in submodule build files instead of centralizing them in the root build configuration's dependency management section. As a result, developers can accidentally introduce version declarations in the wrong place, leading to inconsistencies and making project-wide dependency upgrades harder to manage.

We need a new Maven Enforcer custom rule that automatically detects and rejects these violations at build time.

## Expected Behavior

- When the rule runs against the root build file, it should flag any dependency in the dependency management section that uses a hardcoded literal version (rather than a property reference). Property-based versions are acceptable in the root build file.
- When the rule runs against a submodule build file, it should flag any dependency that declares a version at all — including property-based versions. Submodules must not specify versions for their dependencies; all version management must happen at the root level.
- The rule must support a configurable list of modules that should be excluded from enforcement (e.g., plugin directories), so those modules are silently skipped.
- When a violation is detected, the build must fail with an error message that includes a reference to the project's dependency management documentation.

## Why This Matters

Centralizing dependency version management in the root build configuration ensures consistency across the entire project, makes security audits simpler, and means that upgrading a dependency requires a change in only one place. Automated enforcement ensures this policy is actually followed as the project grows and new contributors join.
